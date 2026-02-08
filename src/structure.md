# How to read this tutorial

Пока каждый раздел — это отдельная статья о Woodpecker CI. Можно читать любую в зависимости, что интереснее, пример с rust web app или с мобильным приложением.

Для удобной работы, советую использовать [mise](https://mise.jdx.dev/).
В корне примеров есть файл mise.toml. В нем описано, что нам потребуется woodpecker-cli

По умолчанию нам потребуется woodpecker-cli.

Чтобы установить глобально:

```bash
mise use --global aqua:woodpecker-ci/woodpecker/woodpecker-cli
```

Либо в файле mise.toml должна быть строчка с установкой утилиты.
В таком случае при переходе в папку с таким toml
у вас будет доступна утилита woodpecker-cli.

```toml
[tools]
"aqua:woodpecker-ci/woodpecker/woodpecker-cli" = "latest"
```
