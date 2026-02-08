# Full Example 1: Rust web app

Итак разберемся как сделать сценарии woodpecker CI для простого веб приложения на Rust.
Само веб приложение можно скачать с [github](https://github.com/Partysun/woodpecker-ci-demo). Это фактически hello world на axum, с двумя endpoints / и /health.

_Написание веб приложения на Rust не является предметом нашего разговора._

Для удобной работы, советую использовать [mise](https://mise.jdx.dev/). В корне проекта есть файл mise.toml.
В нем описано, что нам потребуется woodpecker-cli и hurl. [Hurl](https://hurl.dev)
это утилита для запуска http запросов по файлу сценария. Мы будем его использовать для имитации интеграционных тестов. С помощью mise нужные утилиты будут установлены автоматически и будут доступны только когда вы находитесь в папке проекта.

```bash
curl https://mise.run | sh
mise trust mise.toml
mise install
```

Мы будем работать с docker и docker должен быть установлен в системе. И это проект на Rust, так что установить Rust тоже необходимо.

Проверим, что проект запускается и тесты в main модуле на Rust работают.

```bash
cargo check && cargo test
```

Также проверим, можем ли мы поднять docker с помощью compose и запустим hurl тесты:

```bash
docker compose up -d && mise test-hurl
```

Если в обоих случаях все работает и веб сервер поднимается, то перейдем к пониманию сценариев или workflow для Woodpecker CI.
Для того чтобы запустить сценарии Woodpecker, вам не надо даже настраивать сервер CI, можно начать без всего, но woodpecker-cli потребуется.

Структура наших сценариев на Woodpecker CI:

```bash
❯ tree .woodpecker
/.woodpecker
├── deploy.yaml
├── docker.yaml
└── test.yaml
```

[Source of example project](https://github.com/Partysun/woodpecker-ci-demo)

#### test.yaml

```yaml
matrix:
  RUST: [stable, nightly]

when:
  - event: [push, pull_request]
    branch: master
  - event: manual

labels:
  backend: docker
  platform: linux/amd64

steps:
  - name: build
    image: rust
    environment:
      CARGO_TERM_COLOR: always
    commands:
      - rustup default $RUST
      - echo "Building Cargo Demo Project"
      - cargo build

  - name: test
    image: rust
    environment:
      CARGO_TERM_COLOR: always
    commands:
      - rustup default $RUST
      - cargo test
```

Запустить этот сценарий можно абсолютно без проблем локально.

```bash
woodpecker-cli exec .woodpecker/test.yaml --repo-path .
```

> Нужно обязательно указать "--repo-path ." если вы запускаете команду в корне проекта.
> Иначе CLI попробует запустить сценарий из папки .woodpecker
> Или воспользоваться тасками прописанными в mise.toml.

```bash
mise test
```

Чтобы посмотреть все возможные таски:

```bash
mise tasks

Name       Description
build      Build Docker image, run integration tests, and push to registry using Woodpecker CI
lint       Format and lint Rust code
test       Run tests using Woodpecker CI
test-hurl  Run integration tests using Hurl against local server (http://localhost:4000)
```

Интересным в этом простом pipeline отмечу [matrix](https://woodpecker-ci.org/docs/next/usage/matrix-workflows)

Matrix позволяет запустить билды для разных версий images, зависимостей, компиляторов.

#### Условия When

Для того чтобы сценарий можно было запустить локально, мы должны указать event: manual. Мы можем гибко организовать условия запуска для определенных веток репозитория и событиях на этих ветках, можно также проверять переменные окружения на определенные значения.

#### Теперь о [Labels](https://woodpecker-ci.org/docs/next/usage/workflow-syntax#labels)

Это тоже условия для запуска билда, но они выполняются не при локальном запуске, а на сервере woodpecker CI. Их цель определить на какой ноде агента будет выполнена сборка. Можно к примеру указать:

```yaml
labels:
  node: production
```

И если в [конфигурации ноды агента](https://woodpecker-ci.org/docs/next/administration/configuration/agent#agent_labels) есть

```yaml
WOODPECKER_AGENT_LABELS: "node=production"
```

То сценарий запустится только на этой одной ноде.
