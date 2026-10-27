# 0003. Where the service runs

**Status / Статус:** Accepted

## Context / Контекст

Everyone must run and test the service the same way, without "works on my machine".

## Options / Варианти

- **Directly with Python on each laptop.** Pro: fastest start. Con: different OS and Python versions per person.
- **A Docker container, in GitHub Codespaces or locally.** Pro: the same image everywhere; Codespaces needs no install. Con: everyone has to learn Docker.
- **A cloud VM.** Pro: reachable from the internet. Con: costs money and setup time now.

## Decision / Решение

The service runs as a Docker container, built and started in GitHub Codespaces (or locally with Docker). Kubernetes and the cloud come later, in Labs 8 and 9.

## Consequences / Последствия

Every lab builds on the same image; the Dockerfile lives in the repository and is reviewed like code.
