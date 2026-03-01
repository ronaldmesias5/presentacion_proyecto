# Instrucciones para Subir a GitHub

## Paso 1: Crear un repositorio en GitHub

1. Inicia sesión en [GitHub](https://github.com)
2. Haz clic en el ícono `+` en la esquina superior derecha
3. Selecciona "New repository"
4. **Nombre del repositorio:** `presentacion_proyecto`
5. **Descripción:** Sistema de Gestión y Producción de Calzado
6. Selecciona "Public" o "Private" según prefieras
7. **NO marcques** "Add a README file", "Add .gitignore" ni "Choose a license" (ya tenemos nuestros archivos)
8. Haz clic en "Create repository"

## Paso 2: Conectar tu repositorio local a GitHub

Después de crear el repositorio en GitHub, verás una pantalla con instrucciones. 
Copia y ejecuta el siguiente comando en PowerShell (en la carpeta del proyecto):

```powershell
git remote add origin https://github.com/TU_USUARIO/presentacion_proyecto.git
git branch -M main
git push -u origin main
```

**Reemplaza `TU_USUARIO` con tu nombre de usuario de GitHub.**

### Alternativa con SSH (si tienes SSH configurado):

```powershell
git remote add origin git@github.com:TU_USUARIO/presentacion_proyecto.git
git branch -M main
git push -u origin main
```

## Paso 3: Proporcionar credenciales (si usas HTTPS)

Git te pedirá tus credenciales de GitHub:
- **Usuario:** Tu nombre de usuario de GitHub
- **Contraseña:** Tu token de acceso personal (no tu contraseña)

### Crear un Token de Acceso Personal:

1. Ve a GitHub → Settings → Developer settings → Personal access tokens
2. Haz clic en "Generate new token"
3. Dale un nombre (ej: "git-cli")
4. Selecciona los permisos: `repo` (full control of private repositories)
5. Haz clic en "Generate token"
6. **Copia el token** (solo lo verás una vez)
7. Cuando Git pida contraseña, pega el token

## Paso 4: Verificar que se subió correctamente

```powershell
git remote -v
```

Deberías ver:
```
origin  https://github.com/TU_USUARIO/presentacion_proyecto.git (fetch)
origin  https://github.com/TU_USUARIO/presentacion_proyecto.git (push)
```

## Archivos en el Repositorio

El repositorio contiene:

- **requisitos_funcionales.md** (98.52 KB)
  - 35 Requisitos Funcionales (RF-001 a RF-035)
  - Descripción detallada de cada requerimiento
  - Controles, restricciones y criterios de aceptación

- **historias_de_usuario.md** (29.34 KB)
  - 35 Historias de Usuario (HU-001 a HU-035)
  - Narrativa formato: "Como X, Quiero Y, Para Z"
  - Criterios de aceptación para cada historia

- **casos_de_uso.md** (86.35 KB)
  - 35 Casos de Uso (CU-001 a CU-035)
  - Flujos principales y alternativos detallados
  - Pre y postcondiciones

- **tareas_historias_de_usuario.md** (60.21 KB)
  - Tareas específicas para cada historia (4-7 tareas por historia)
  - Criterios de aceptación técnicos (CA1, CA2, etc.)
  - Implementación paso a paso

- **readme.md** (7.39 KB)
  - Descripción general del proyecto

## Próximos Push

Después de hacer cambios locales, usa:

```powershell
git add .
git commit -m "Descripción del cambio"
git push origin main
```

## Problemas Comunes

### Error: "fatal: 'origin' does not appear to be a 'git' repository"

Asegúrate de estar en la carpeta correcta:
```powershell
cd "c:\Users\ronal\bootcamp\presentacion_proyecto"
```

### Error de autenticación

- Si usas HTTPS: verifica que estés usando un token, no tu contraseña
- Si usas SSH: verifica que tengas las claves SSH configuradas correctamente

### Cambios rechazados

Si GitHub rechaza tus cambios (conflictos, etc.):
```powershell
git pull origin main
git push origin main
```

---

¡Tu repositorio está listo para ser subido a GitHub! 🚀
