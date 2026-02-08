# Introduction

Hi there, I'm [Yura Zatsepin](https://zatsepin.dev). This is a tutorial for [Woodpecker](https://woodpecker-ci.org/)—a CI/CD system.

This tutorial exists because I'm trying to find a good CI for "non K8S world", fully locally and which
can give me a fresh perspective. This is all about Woodpecker CI, which was formerly a fork of Drone CI.
Drone CI I used long time ago on couple projects. It was a real lightweight solution after Jenkins and TeamCity
where I had my experience before.

I know Woodpecker CI doesn't yet offer many battle-tested features from modern commercial CI, but it's an open source
and very niche product. It's based on ideas already applied in Gitlab CI and GitHub Actions.
But the core advantage from Drone is still here: you can start working with CI locally. You don't need
external solutions for this like Gitlab local runner or GitHub Actions - act CLI. Here you can install Woodpecker-cli
and write some YAML files and you're ready. Our workflows can be run locally. If we see a broken pipeline on Woodpecker server,
we can download metadata from a failed build and re-run it locally.
In general, you can start writing CI workflows when starting a new project from day 0 without any server at all.
Yes, GitHub Actions is available. And it's actually great. But when you need a self-hosted, fully open CI solution
for modern world dev experience this is what you probably need Woodpecker CI.

I am writing tutorials here after some personal research about this tool. Actually, I already have production pipelines on this CI
and I've encountered some problems with hosting it and deploying certain things. [The docs](https://woodpecker-ci.org/docs/next/intro) are great, but they don't address
all the challenges I faced. I find this book a good place to save my thoughts for the future and anyone who wants to try Woodpecker.

> In the current version, this book is written in Russian language.
