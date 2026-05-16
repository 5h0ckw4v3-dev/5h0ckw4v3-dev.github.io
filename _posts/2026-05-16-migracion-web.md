---
title: "Migrando web Jekyll/Chirpy legacy a una versión moderna"
author: 5h0ckw4v3-dev
date: '2026-05-16 10:00:00 +0100'
categories:
  - Linux
tags:
  - Linux
  - DevOps
  - Terminal
render_with_liquid: false
published: true
---

![command](/assets/img/common/codigo.jpg)



## INTRODUCCIÓN

Hace unos días, al realizar un commit sobre mi web personal basada en Jekyll y el tema Chirpy, el pipeline de GitHub Actions comenzó a fallar de forma inesperada durante la fase de instalación de dependencias Ruby.

La web llevaba tiempo funcionando sin problemas y, sinceramente, no había tocado la parte del stack Jekyll/Chirpy desde hacía bastante tiempo. El problema terminó siendo una combinación de varios factores:

- Workflow antiguo de GitHub Actions
- Uso de versiones flotantes de Ruby
- Incompatibilidad de `ffi` con Ruby 4
- Layouts heredados de versiones antiguas de Chirpy

Aproveché el incidente para realizar una actualización completa y dejar la web en una base mucho más moderna y mantenible.

## El problema inicial

El fallo aparecía durante el `bundle install` del workflow de GitHub Actions:

```plaintext
ffi-1.17.2-x86_64-linux-gnu requires ruby version < 3.5.dev, >= 2.5,
which is incompatible with the current version, 4.0.2

El workflow que tenía configurado era bastante antiguo:

```plaintext
- name: Setup Ruby
  uses: ruby/setup-ruby@v1
  with:
    ruby-version: ruby
    bundler-cache: true
```
El problema aquí es bastante claro una vez lo analizas.

Al usar:
```plaintext
ruby-version: ruby
```
GitHub Actions resuelve automáticamente una versión disponible del runner. En este caso, comenzó a utilizar Ruby 4.0.2.

La gema ffi, utilizada indirectamente por parte del ecosistema Jekyll, todavía no soportaba Ruby 4, así que el pipeline fallaba antes siquiera de comenzar el build.

## Actualizando el workflow de GitHub Actions

Lo primero fue modernizar completamente el workflow de despliegue.

En lugar del pipeline antiguo basado en tools/deploy.sh, decidí utilizar el flujo moderno de GitHub Pages basado en:

actions/configure-pages
actions/upload-pages-artifact
actions/deploy-pages

Además, fijé explícitamente la versión de Ruby para evitar futuras roturas por cambios automáticos del runner.

El nuevo workflow quedó así:
```plaintext
name: "Build and Deploy"

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.4
          bundler-cache: true

      - name: Build site
        run: bundle exec jekyll b

      - name: Upload site artifact
        uses: actions/upload-pages-artifact@v4
        with:
          path: _site

  deploy:
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```
Con esto, el problema de Ruby desapareció inmediatamente.

## Actualizando Chirpy

La web seguía utilizando una versión bastante antigua del tema:
```plaintext
gem "jekyll-theme-chirpy", "~> 5.2", ">= 5.2.1"

Aproveché para actualizar directamente a la rama moderna del proyecto:
```plaintext
gem "jekyll-theme-chirpy", "~> 7.5", ">= 7.5.0"
```
Después ejecuté:
```plaintext
bundle update jekyll-theme-chirpy
```
Y posteriormente validé el build localmente:
```plaintext
bundle exec jekyll b
bundle exec jekyll s
```
## El problema oculto: layouts heredados

Aquí apareció el segundo problema importante.

Aunque el tema ya estaba actualizado, Jekyll comenzó a fallar con el siguiente error:
```plaintext
Could not locate the included file 'comments.html'
```
A primera vista parecía un problema interno del tema, pero realmente el origen estaba en un override antiguo heredado de versiones anteriores de Chirpy.

En mi caso existía este archivo:
```plaintext
_layouts/post.html
```
El contenido era prácticamente un layout stock antiguo del tema:
```plaintext
tail_includes:
  - related-posts
  - post-nav
  - comments
```
Las versiones modernas de Chirpy ya no utilizan comments.html de esa manera, así que el layout local estaba sobrescribiendo el layout moderno del tema y rompiendo el renderizado.

La solución fue simplemente eliminar el override heredado:
```plaintext
mv _layouts/post.html backup_layouts/post.html.bak
```
Tras eliminarlo, el build volvió a funcionar correctamente.

## Estrategia de migración

Aunque la web es relativamente pequeña, preferí hacer toda la actualización siguiendo un flujo bastante conservador.

Primero creé una rama específica para la migración:
```plaintext
git checkout -b upgrade-chirpy
```
Después:

- Actualización del workflow
- Actualización de Chirpy
- Validación local
- Validación en GitHub Actions
- Merge posterior a main

Evitar tocar directamente producción simplifica muchísimo el troubleshooting y reduce el riesgo de dejar la web inutilizable durante la migración.

## Resultado final

Después de la actualización:

- Chirpy quedó actualizado a la versión 7.5
- GitHub Actions utiliza un workflow moderno y mantenible
- Ruby quedó fijado explícitamente a 3.4
- Se eliminaron layouts legacy incompatibles
- El pipeline volvió a ser completamente reproducible

Además, la web quedó preparada para futuras actualizaciones con bastante menos riesgo que antes.

## Conclusiones

Este tipo de problemas son bastante comunes en proyectos que llevan tiempo funcionando sin tocarse demasiado.

Mientras todo funciona, es fácil dejar:

- Workflows antiguos
- Versiones flotantes
- Layouts heredados
- Dependencias desactualizadas

El problema aparece cuando alguna pieza del ecosistema cambia y toda la cadena comienza a romperse.

En este caso, el detonante fue Ruby 4, pero el verdadero problema era que el stack completo necesitaba una actualización controlada.

A veces merece la pena aprovechar un fallo puntual para modernizar la infraestructura antes de que el problema sea mucho mayor.
