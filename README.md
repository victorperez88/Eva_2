# Demo CI + BDD + Performance (Java/Maven)

Este repositorio demuestra un flujo básico-profesional de **Integración Continua**, **BDD**, **métricas y reporting**, **tests atómicos**, y **buenas prácticas**.

## Objetivos
- Gestionar versiones con Git (ramas y commits claros).
- Configurar un proyecto Maven con **JUnit 5** y **Cucumber**.
- Implementar **tests unitarios atómicos** (suma y resta) y **escenarios BDD** (login).
- Ejecutar todo en un **pipeline CI** (GitHub Actions), con **reportes navegables** (JUnit, Cucumber, JaCoCo, Gatling).
- Añadir una **prueba básica de performance** con Gatling y mostrar **indicadores** clave.
- Documentar decisiones, comandos y estructura.


---

## 1) Flujo Git recomendado

```bash
git init
git branch -m main
git add .
git commit -m "chore: primera versión del esqueleto CI+BDD"

# trabajo por features
git checkout -b feat/unit-tests
# (editar archivos) 
git add -A && git commit -m "test: agrega pruebas atómicas de suma y resta"

git checkout -b feat/bdd-login
# (editar archivos BDD)
git add -A && git commit -m "feat(bdd): escenarios login + steps + reporte HTML"

git checkout -b feat/perf-gatling
git add -A && git commit -m "perf: simulación Gatling y plugin en verify"

# merge mediante PR en GitHub (recomendado)
```

**Evidencia git - commits**
![git-commits](docs/img)

---

## 2) Estructura del proyecto

```text
.
├─ src
│  ├─ main/java/com/example/app
│  │  ├─ Calculator.java
│  │  └─ AuthService.java
│  ├─ test/java/com/example/app
│  │  ├─ CalculatorAddTest.java
│  │  └─ CalculatorSubtractTest.java
│  ├─ test/java/bdd
│  │  ├─ RunCucumberTest.java
│  │  └─ StepDefinitions.java
│  └─ test/resources/features
│     └─ login.feature
├─ src/test/scala/gatling/LoginSimulation.scala
├─ pom.xml
├─ .github/workflows/ci.yml
├─ .gitignore
└─ README.md
```

- **Atomicidad**: cada test verifica un comportamiento único.
- **Independencia**: no comparten estado; instancian sus propias dependencias.
- **Convenciones Maven**: `src/main/java` para código de prod, `src/test/...` para pruebas.


---

## 3) Dependencias clave (Maven)

- **JUnit 5** (`org.junit.jupiter:junit-jupiter`)
- **Cucumber** (`io.cucumber:cucumber-java` + `cucumber-junit-platform-engine`)
- **JaCoCo** (plugin de cobertura)
- **Gatling** (plugin de performance)

Ejecuta localmente:

```bash
mvn -q test        # unit + BDD
mvn -q verify      # + jacoco + gatling
open target/cucumber-report.html  # reporte BDD navegable
open target/site/jacoco/index.html  # cobertura
# Informe Gatling queda en target/gatling/<sim>/index.html
```

**Evidencia local :**
![local-tests](docs/img)



## 4) BDD

- **Feature**: `src/test/resources/features/login.feature`
- **Steps**: `src/test/java/bdd/StepDefinitions.java`
- **Runner**: `RunCucumberTest.java` con plugin HTML/JSON

Ejemplo de escenario (Gherkin):

```gherkin
Scenario: Login exitoso con credenciales válidas
  When intento iniciar sesión con usuario "alice" y contraseña "1234"
  Then el resultado del login debe ser "true"
```

**Reporte Cucumber**: `target/cucumber-report.html`

**Evidencia CI:**
![ci-tests](docs/img)



## 5) Pipeline CI (GitHub Actions)

- Compila y ejecuta **unit + BDD + cobertura + performance** en cada `push` o `PR`.
- Publica artefactos: **Surefire**, **Cucumber**, **JaCoCo**, **Gatling**.
- **Alertas**: ejemplo de integración con Slack por webhook (usar `SLACK_WEBHOOK_URL` en Secrets).

Archivo: `.github/workflows/ci.yml`

---

## 6) Performance con Gatling

- Simulación: `LoginSimulation.scala`
- Indicadores a monitorear:
  - **TPS (Throughput)**: usuarios/seg.
  - **Latencia**: p50, p90, p95.
  - **Errores**: tasa de fallos / códigos HTTP != 2xx/3xx.
- Reporte HTML: `target/gatling/<sim>/index.html`

**Evidencia perf-report:**
![perf-report](docs/img)

---

## 7) Métricas & Dashboard

- **Funcionales**: JUnit/Surefire + Cucumber (pasos, escenarios, duración).
- **Calidad**: **JaCoCo** (líneas/branches cubiertos).
- **Performance**: Gatling (latencias y throughput).
- Cómo llevar a un **dashboard** del pipeline:
  1. Publicar artefactos (ya se hace en CI).
  2. Agregar un paso que compile un markdown con KPIs y lo envíe al **Job Summary**.
  3. Opcional: publicar a **GitHub Pages** o enviar a **Grafana**/ELK usando webhooks.

---

## 8) Alertas automáticas

- Paso en CI que **notifica a Slack** ante `failure()`.
- Alternativas: correo via Action, abrir issue automático, o push a Prometheus alertmanager.

---

## 9) Buenas prácticas aplicadas

- **Commits pequeños y con mensajes claros**.
- **Tests atómicos e independientes**.
- **Estructura estándar de Maven**.
- **Pipeline por push/PR** para feedback continuo.
- **Reportes navegables** y **artefactos compartidos** con el equipo.
- **Comentarios y documentación** en el código y este README.

---

## 10) Cómo ejecutar todo

1. Instala JDK 17 + Maven 3.9+.
2. Clona este repo y crea tu propio remoto en GitHub.
3. Ejecuta local: `mvn verify` y abre los reportes.
4. Sube cambios y verifica los artefactos del pipeline en **Actions**.
5.  las imágenes en `docs/img` 
---



