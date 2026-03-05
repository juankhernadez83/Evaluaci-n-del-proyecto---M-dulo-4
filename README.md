# 🗂️ Angular Bootstrap App v3.0

Proyecto académico full-stack que integra **Angular 17**, **Bootstrap 5**, **NgRx**, **Dexie**, **API Express**, **Mapbox GL**, **Cypress** y **CircleCI**, cubriendo los módulos de ciclo de vida, animaciones, directivas personalizadas, testing unitario y testing e2e.

---

## 🚀 Cómo ejecutar

### Requisitos previos
- Node.js >= 18
- Angular CLI: `npm install -g @angular/cli`
- (Opcional) Cypress: incluido en devDependencies

### 1. Instalar dependencias del frontend
```bash
cd angular-bootstrap-app-v4
npm install
```

### 2. Levantar la API Express
```bash
cd backend
npm install
node server.js
# ✅ API corriendo en http://localhost:3000
```

### 3. Levantar Angular
```bash
# En otra terminal, desde la raíz del proyecto
npm start
# ✅ App en http://localhost:4200
```

### 4. Credenciales de acceso
| Campo       | Valor   |
|-------------|---------|
| Usuario     | `admin` |
| Contraseña  | `1234`  |

### 5. Ejecutar tests unitarios (Jasmine / Karma)
```bash
npm test
# o en modo headless:
npm test -- --watch=false --browsers=ChromeHeadless
```

### 6. Ejecutar tests e2e (Cypress)
```bash
# Modo interactivo (con UI)
npm run cypress:open

# Modo headless (CI)
npm run cypress:run
```

### 7. Verificar paquete Mapbox instalado
```bash
npm ls ngx-mapbox-gl
# angular-bootstrap-app@3.0.0
# └── ngx-mapbox-gl@10.x.x
```

---

## 📁 Estructura del proyecto

```
angular-bootstrap-app-v4/
│
├── .circleci/
│   └── config.yml                         ← Req #10: Pipeline CI/CD
│
├── backend/
│   ├── server.js                          ← API Express: GET + POST /api/tasks
│   └── package.json
│
├── cypress/
│   ├── e2e/
│   │   ├── 01-login.cy.js                 ← Test e2e: autenticación
│   │   ├── 02-tareas.cy.js                ← Test e2e: formulario y validaciones
│   │   └── 03-votaciones-tracking.cy.js   ← Test e2e: votos y tracking
│   └── support/
│       └── e2e.js                         ← Comando custom cy.login()
│
├── cypress.config.js
├── karma.conf.js
├── package.json                           ← ngx-mapbox-gl, @types/mapbox-gl@0.49.0, cypress
├── angular.json
├── tsconfig.json
├── tsconfig.app.json
├── tsconfig.spec.json
│
└── src/
    ├── test.ts                            ← Punto de entrada Karma
    ├── index.html
    ├── main.ts
    ├── styles.scss                        ← @import Bootstrap
    │
    └── app/
        ├── app.module.ts                  ← BrowserAnimationsModule, NgxMapboxGLModule, trackingReducer
        ├── app-routing.module.ts          ← Rutas: /mapa, /tracking (protegidas por AuthGuard)
        ├── app.component.html             ← <router-outlet> + nav actualizada
        │
        ├── auth/
        │   ├── auth.service.ts            ← estaLogueado(), login(), logout()
        │   └── auth.guard.ts              ← canActivate() protege rutas
        │
        ├── core/
        │   ├── tokens.ts                  ← InjectionToken APP_CONFIG
        │   └── logger.ts                  ← useClass / useExisting
        │
        ├── db/
        │   └── app-database.ts            ← Dexie — IndexedDB
        │
        ├── directives/
        │   └── click-tracker.directive.ts ← Req #4 #5 #6: tracking de clicks con ElementRef
        │
        ├── components/
        │   ├── header/
        │   ├── task-form/                 ← @Output, FormBuilder, validaciones personalizadas
        │   └── animated-card/             ← Req #3: animación con @trigger cuando "activo" cambia
        │
        ├── services/
        │   └── task-api.service.ts        ← HttpClient + NgRx dispatch + Dexie
        │
        ├── validators/
        │   └── custom.validators.ts       ← minPalabras(n), noCaracteresEspeciales
        │
        ├── models/
        │   └── item.model.ts              ← Item, VoteItem
        │
        ├── store/
        │   ├── actions/
        │   │   ├── task.actions.ts        ← agregarTarea, eliminarTarea, votarPositivo…
        │   │   └── tracking.actions.ts    ← registrarClick, resetTracking
        │   ├── reducers/
        │   │   ├── task.reducer.ts
        │   │   ├── vote.reducer.ts
        │   │   ├── tracking.reducer.ts    ← Req #7: contadores por tag
        │   │   └── reducers.spec.ts       ← Req #8: tests Jasmine de los 3 reducers
        │   └── selectors/
        │       └── app.selectors.ts       ← selectTrackingCounters, selectTotalClicks
        │
        └── pages/
            ├── login-page/
            ├── home-page/                 ← [appClickTracker] en botón eliminar
            ├── votes-page/
            ├── map-page/                  ← Req #1 #2: Mapbox con markers y popups
            └── tracking-page/             ← Req #6 #7: directiva + contadores reactivos
```

---

## ✅ Cumplimiento de requisitos

### Módulo 4 — Mapbox, Animaciones, Directivas, Testing, CI

| # | Requisito | Archivo |
|---|-----------|---------|
| 1 | `ngx-mapbox-gl` y `@types/mapbox-gl@0.49.0` instalados | `package.json` |
| 2 | Marker en el mapa con popup al hacer click | `pages/map-page/map-page.component.html` — `<mgl-marker>` + `<mgl-popup *ngIf>` |
| 3 | Componente con animación al cambiar una propiedad | `components/animated-card/` — `@trigger('cardState')` en `ngOnChanges` |
| 4 | Directiva personalizada para trackear clicks | `directives/click-tracker.directive.ts` |
| 5 | Directiva recibe `ElementRef` por DI y se suscribe a eventos DOM | `click-tracker.directive.ts` — `constructor(private el: ElementRef)` + `addEventListener` |
| 6 | Directiva en templates con `trackTag` | `tracking-page.component.html`, `home-page.component.html`, `map-page.component.html` |
| 7 | Redux actualiza contadores de tracking reactivamente | `store/actions/tracking.actions.ts` + `store/reducers/tracking.reducer.ts` + `async` pipe |
| 8 | Spec Jasmine para todos los reducers | `store/reducers/reducers.spec.ts` — 15+ tests sobre `taskReducer`, `voteReducer`, `trackingReducer` |
| 9 | Cypress con 3+ tests propios | `cypress/e2e/01-login.cy.js`, `02-tareas.cy.js`, `03-votaciones-tracking.cy.js` |
| 10 | Integración con CircleCI | `.circleci/config.yml` |

### Módulos anteriores (incluidos en este proyecto)

| Módulo | Requisitos cubiertos |
|--------|----------------------|
| Módulo 1 | Bootstrap npm, `@import` global, `{{ }}`, `*ngFor`, `*ngIf`, `@HostBinding`, formulario con `#`, `agregarItem()` reactivo |
| Módulo 2 | `@Output` EventEmitter, rutas con redirect, `<router-outlet>`, `FormBuilder`/`FormGroup`, `formControlName`, validación personalizada `minPalabras(n)`, `hasError`, reducer NgRx, votos positivo/negativo |
| Módulo 3 | `AuthGuard`, `canActivate`, `InjectionToken`, `useClass`, `useExisting`, API Express GET/POST, `HttpClient`, NgRx dispatch post-API, Dexie IndexedDB |

---

## 🔌 Endpoints de la API Express

| Método   | URL              | Descripción                                     |
|----------|------------------|-------------------------------------------------|
| `GET`    | `/api/tasks`     | Retorna el array de tareas en memoria           |
| `POST`   | `/api/tasks`     | Agrega tarea (body JSON: `nombre`, `descripcion`, `categoria`) |
| `DELETE` | `/api/tasks/:id` | Elimina tarea por id                            |

**Ejemplo de POST:**
```bash
curl -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Nueva tarea","descripcion":"Descripción de prueba","categoria":"Estudio"}'
```

---

## 🧪 Testing

### Tests unitarios — Jasmine / Karma

Archivo: `src/app/store/reducers/reducers.spec.ts`

Los reducers son **funciones puras**: reciben `(state, action)` y retornan un nuevo estado **sin mutar el original**.

```bash
npm test
```

| Suite            | Casos de prueba |
|------------------|-----------------|
| `taskReducer`    | Estado inicial · `cargarTareas` · `agregarTarea` · `eliminarTarea` · inmutabilidad |
| `voteReducer`    | Estado inicial · `agregarVoteItem` · `eliminarVoteItem` · `votarPositivo` · `votarNegativo` · inmutabilidad |
| `trackingReducer`| Estado inicial · `registrarClick` nuevo tag · incremento · múltiples tags · `resetTracking` · inmutabilidad |

### Tests e2e — Cypress

```bash
npm run cypress:open   # modo interactivo
npm run cypress:run    # modo headless
```

| Archivo                          | Escenarios cubiertos |
|----------------------------------|----------------------|
| `01-login.cy.js`                 | Redirect sin auth · error con credenciales incorrectas · login exitoso · logout |
| `02-tareas.cy.js`                | Formulario visible · errores de validación · `minPalabras` · estado válido · navegación |
| `03-votaciones-tracking.cy.js`   | Votos positivos/negativos · agregar propuesta · contadores de tracking · reset |

---

## 🗺️ Mapbox

El componente `map-page` utiliza `ngx-mapbox-gl`. Para producción, reemplaza el token demo en `map-page.component.html` con tu token real de [mapbox.com](https://www.mapbox.com):

```html
<mgl-map [accessToken]="'pk.eyJ1IjoiTVlfVVNFUi...'"  ...>
```

---

## ⚙️ Pipeline CircleCI

Archivo: `.circleci/config.yml`

```
install
   ├── build          (ng build --prod)
   └── unit-tests     (karma + jasmine)
         └── e2e-tests    (cypress run)
```

Para activarlo, conecta el repositorio en [app.circleci.com](https://app.circleci.com) y el pipeline se ejecutará automáticamente en cada `git push`.

---

## 🏗️ Inyección de dependencias

```typescript
providers: [
  // InjectionToken con useValue
  { provide: APP_CONFIG,        useValue:    APP_CONFIG_VALUE     },

  // useClass: LoggerService → ConsoleLoggerService
  { provide: LoggerService,     useClass:    ConsoleLoggerService  },

  // useExisting: compatibles por herencia de BaseLoggerService
  ConsoleLoggerService,
  { provide: FileLoggerService, useExisting: ConsoleLoggerService },
]
```

---

## 🔄 Flujo completo del Tracking de Clicks

```
Usuario hace click en un elemento con [appClickTracker]
    ↓
ClickTrackerDirective captura el evento con addEventListener (ElementRef)
    ↓
Lee el valor de [trackTag] del Input de la directiva
    ↓
Despacha registrarClick({ tag }) al store NgRx
    ↓
trackingReducer incrementa contadores[tag] y totalClicks
    ↓
selectTrackingCounters y selectTotalClicks emiten nuevo valor
    ↓
tracking-page.component.html se actualiza reactivamente con async pipe
    ↓
app-animated-card anima la transición con @trigger('cardState')
```

---

## 🧱 Tecnologías utilizadas

| Tecnología          | Versión  | Uso                                        |
|---------------------|----------|--------------------------------------------|
| Angular             | 17       | Framework frontend principal               |
| Bootstrap           | 5.3      | Estilos y componentes UI                   |
| NgRx Store          | 17       | Estado global con Redux                    |
| Dexie.js            | 3.2      | Base de datos IndexedDB en el navegador    |
| Express             | 4.18     | API REST sin persistencia                  |
| ngx-mapbox-gl       | 10       | Mapa interactivo con markers y popups      |
| @types/mapbox-gl    | 0.49.0   | Tipos TypeScript para Mapbox               |
| Cypress             | 13       | Tests end-to-end                           |
| Jasmine / Karma     | 5.1 / 6.4| Tests unitarios                            |
| CircleCI            | 2.1      | CI/CD pipeline                             |
| RxJS                | 7.8      | Programación reactiva                      |
| TypeScript          | 5.2      | Tipado estático                            |

---

## 📝 Notas

- La API Express **no persiste datos**: se reinicia con el servidor.
- **Dexie** persiste en el navegador (IndexedDB) y sobrevive recargas.
- La autenticación es **simulada**: no requiere backend de auth.
- Instala [Redux DevTools](https://chromewebstore.google.com/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd) en Chrome para inspeccionar el store NgRx en tiempo real.
- El token de Mapbox incluido es de demo: reemplázalo con tu propio token para uso en producción.
