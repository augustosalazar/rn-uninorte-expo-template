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

## Empezar

```bash
npm install
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

```
App.tsx                  # PaperProvider + NavigationContainer con el tema
index.tsx                # Punto de entrada (registerRootComponent)
src/
  core/                  # Servicios compartidos (preferencias locales)
  features/              # Pantallas por funcionalidad (home, ...)
  theme/                 # Colores claro/oscuro y temas de Paper
__tests__/               # Pruebas
```

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
