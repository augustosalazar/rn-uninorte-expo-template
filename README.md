# rn-uninorte-expo-template

Template base para el curso de React Native con Expo (Uninorte).

## Stack

- [Expo SDK 57](https://docs.expo.dev/) · React Native 0.86 · React 19.2
- Navegación con [React Navigation 7](https://reactnavigation.org/) (`NavigationContainer`, sin Expo Router)
- UI con [React Native Paper](https://callstack.github.io/react-native-paper/) (Material 3, tema claro/oscuro)
- Persistencia local con AsyncStorage
- Pruebas con Jest (`jest-expo`) y React Native Testing Library

## Requisitos

- **Node.js 24 LTS** (recomendado). Mínimo soportado: 22.13 o 24.3. Con nvm o fnm basta `nvm use` (lee `.nvmrc`).

## Crear un proyecto desde el template

```bash
npx create-expo-app@latest nombre-proyecto --template https://github.com/augustosalazar/rn-uninorte-expo-template
```

Esto descarga el template, renombra el proyecto (`name` y `slug`), instala las dependencias e inicializa un repositorio git.

Después de crearlo, cambie el `scheme` en `app.json` (por defecto `myapp`) por uno propio, en minúsculas y sin espacios (por ejemplo `nombreproyecto`). Es el prefijo de los deep links (`nombreproyecto://...`); si dos apps instaladas comparten el mismo scheme, los enlaces pueden abrir la app equivocada.

## Empezar

```bash
cd nombre-proyecto
npx expo start
```

Desde la salida de Expo se puede abrir la app en un emulador de Android, un simulador de iOS, la web o [Expo Go](https://expo.dev/go).

## Scripts

| Comando | Descripción |
|---|---|
| `npm start` | Inicia el servidor de desarrollo |
| `npm run android` / `ios` / `web` | Inicia y abre en la plataforma indicada |
| `npm run lint` | ESLint con la configuración de Expo |
| `npm test` | Jest en modo watch |
| `npm run test:ci` | Jest una sola vez (CI) |

## Estructura

El proyecto sigue una arquitectura limpia por funcionalidad (feature), con inyección de dependencias.

```
App.tsx                      # Providers (DI, features, Paper) + NavigationContainer
index.tsx                    # Punto de entrada (registerRootComponent)
src/
  core/
    di/
      container.ts           # Contenedor de dependencias (singletons por token)
      tokens.ts              # Un token por dependencia
      appContainer.ts        # Raíz de composición: qué implementación usa cada interfaz
      DIProvider.tsx         # Expone el contenedor con useDI()
    iLocalPreferences.ts     # Almacenamiento local (interfaz + implementación AsyncStorage)
  navigation/
    RootNavigator.tsx        # Stack raíz + tabs
    types.ts                 # Tipos de rutas y parámetros (navegación tipada)
  features/
    home/                    # Funcionalidad simple: solo una pantalla
    notes/                   # Funcionalidad de ejemplo con todas las capas
      domain/
        entities/            # Modelos (Note)
        repositories/        # Interfaces de repositorio (qué se puede hacer)
      data/
        datasources/         # Acceso a datos (interfaz + implementación local)
        repositories/        # Implementación del repositorio
      presentation/
        context/             # Estado de la funcionalidad (NotesProvider, useNotes)
        screens/             # Pantallas
  theme/                     # Colores claro/oscuro y temas de Paper
__tests__/                   # Pruebas (unitarias, de pantalla y de flujo)
```

### Reglas de dependencia

- `presentation` → `domain`: las pantallas usan el contexto, y el contexto usa la **interfaz** del repositorio, obtenida con `useDI()`.
- `data` → `domain`: las implementaciones cumplen las interfaces del dominio.
- `domain` no importa nada de `data`, `presentation`, React ni librerías externas.
- Solo `core/di/appContainer.ts` conoce las clases concretas. Para cambiar el almacenamiento (por ejemplo a un backend remoto) se crea otro data source y se cambia ahí; las pantallas no cambian.

### Agregar una funcionalidad

1. Copie `src/features/notes/` con el nuevo nombre y defina la entidad y la interfaz del repositorio en `domain/`.
2. Implemente el data source y el repositorio en `data/`.
3. Agregue sus tokens en `tokens.ts` y regístrelos en `appContainer.ts`.
4. Cree el provider y las pantallas en `presentation/`; agregue el provider en `App.tsx` y las rutas en `navigation/types.ts` y `RootNavigator.tsx`.
5. Para probar pantallas sin almacenamiento real, pase un contenedor con dobles de prueba: `<DIProvider container={...}>` (ver `__tests__/features/notes/NoteListScreen.test.tsx`).

La funcionalidad `notes` es solo un ejemplo: puede borrarla (carpeta, tokens, registro en `appContainer.ts`, provider, rutas y pruebas) cuando ya no la necesite.

## Development build

El proyecto incluye `expo-dev-client`. Para probar en un dispositivo con módulos nativos propios:

```bash
npx expo run:android   # o npx expo run:ios
```

Expo Go sigue funcionando con `npx expo start` mientras no se agreguen librerías nativas fuera del SDK.

## Ícono y splash screen

Las imágenes en `assets/images/` son **placeholders** ("RN" sobre el color primario del tema). Reemplácelas conservando nombre y tamaño; no hace falta tocar `app.json`.

| Archivo | Tamaño | Uso |
|---|---|---|
| `icon.png` | 1024×1024, opaco, sin esquinas redondeadas | Ícono iOS y por defecto |
| `android-icon-foreground.png` | 512×512, fondo transparente, logo dentro del 66 % central | Ícono adaptativo Android (capa frontal) |
| `android-icon-background.png` | 512×512 | Ícono adaptativo Android (fondo) |
| `android-icon-monochrome.png` | 432×432, blanco sobre transparente | Ícono temático Android 13+ |
| `splash-icon.png` | 1024×1024, fondo transparente | Logo del splash (se muestra a 200 px de ancho) |
| `favicon.png` | 48×48 | Web |

Los colores de fondo del splash (claro/oscuro) y del ícono adaptativo se definen en `app.json`. El splash permanece visible mientras `App.tsx` termina de cargar (función `prepare`); agregue ahí lo que la primera pantalla necesite.

Los cambios de ícono y splash solo se ven en un build nativo nuevo (`npx expo run:android` / `run:ios`), no en Expo Go.

## Actualizar Expo

```bash
npx expo install expo@^<versión> --fix
npx expo-doctor
```

Después de actualizar, revise la versión de Node que exige React Native (`engines.node` en `node_modules/react-native/package.json`) y ajuste `engines` en `package.json`, `.nvmrc` y la sección de requisitos.
