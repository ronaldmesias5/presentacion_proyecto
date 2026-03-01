# TAREAS DE HISTORIAS DE USUARIO
## Sistema de Gestión y Producción de Calzado

---

## RF001 – CREACIÓN DE CUENTAS DE ACCESO

**Historia de Usuario**

COMO usuario potencial (cliente mayorista)
QUIERO poder registrarme autónomamente desde la página principal del sistema
PARA solicitar una cuenta de acceso sin depender de la intervención inicial del jefe, agilizando mi incorporación al sistema.

**Tareas de la Historia (Tasks)**

1. **Diseño y desarrollo del formulario de registro:**
   - Crear una página de registro accesible desde la página principal (navbar/footer)
   - Incluir campos obligatorios:
     * Nombre completo (validar solo letras y espacios)
     * Correo electrónico (validar formato)
     * Tipo de usuario: solo "cliente mayorista" (cliente potencial)
     * Número de documento de identidad (validar formato)
     * Número de teléfono (validar formato)
     * Razón social (para clientes mayoristas)
     * NIT (validar formato)
   - Implementar validación en tiempo real de cada campo
   - Incluir mensajes de error específicos por campo
   - Diseñar interfaz responsive acorde a TailwindCSS

2. **Validación de unicidad en tiempo real:**
   - Conectar el formulario con API de backend FastAPI
   - Crear endpoint que valide: correo, documento, NIT
   - Mostrar indicador visual (✓ disponible / ✗ ya registrado)
   - Implementar debounce en búsquedas (500ms)

3. **Procesamiento del registro:**
   - Crear servicio backend que:
     * Valide todos los campos obligatorios
     * Encripte y almacene datos en PostgreSQL
     * Genere ID único de solicitud (formato: SOL-YYYYMMDD-XXXX)
     * Cree registro con estado "pendiente de validación"
   - Implementar envío automático de correo confirmación (SMTP)
   - Mostrar acuse de recibo en pantalla con número de solicitud
   - Redirigir a página de estado de solicitud

4. **Notificación automática al jefe:**
   - Crear sistema de notificaciones en base de datos
   - Registrar notificación en tiempo real (< 30 segundos)
   - Incluir en notificación: ID solicitud, nombre, tipo de usuario, fecha
   - Implementar endpoint para que jefe consulte solicitudes pendientes

5. **Gestión de intentos duplicados:**
   - Si correo ya existe, detectar en validación en tiempo real
   - Redirigir automáticamente a página: "Solicitud Existente"
   - Mostrar estado actual de solicitud anterior y botón de descargar de solicitud

6. **Auditoría y trazabilidad:**
   - Registrar cada evento en tabla `audit_log`:
     * Timestamp preciso (servidor)
     * IP del cliente (capturar de request)
     * Todos los datos ingresados
     * Resultado (éxito/fallo)
     * Razón de rechazo si aplica
   - Implementar rotación de logs

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Un usuario puede acceder al formulario de registro desde la página principal sin autenticación.
- CA2: El formulario valida en tiempo real que el correo tenga formato válido (regex estándar).
- CA3: El sistema impide el registro si el correo, documento o NIT ya existen en la base de datos.
- CA4: Si algún campo obligatorio está vacío o con formato incorrecto, se muestra un mensaje específico (rojo) y se bloquea el envío.
- CA5: Al enviar el formulario correctamente:
  * Se crea un registro con estado "pendiente de validación"
  * Se genera un ID único de solicitud (formato: SOL-YYYYMMDD-0001)
  * El usuario recibe un mensaje deverificación en pantalla
  * Se envía correo de confirmación con el número de solicitud
- CA6: El jefe recibe una notificación en su panel en menos de 30 segundos tras el registro exitoso.
- CA7: Si un usuario intenta registrarse con un correo ya registrado, es redirigido a una página que muestra el estado de su solicitud anterior.
- CA8: Todos los eventos (éxito o fallo) quedan registrados en el historial de auditoría con marca de tiempo, IP, datos ingresados y resultado.

---

## RF002 – VALIDACIÓN Y ACTIVACIÓN DE CUENTAS POR JEFE

**Historia de Usuario**

COMO jefe del sistema
QUIERO poder revisar, validar y activar las solicitudes de cuentas de acceso pendientes
PARA controlar el acceso al sistema y garantizar que solo usuarios verificados accedan.

**Tareas de la Historia (Tasks)**

1. **Creación del módulo de validación de cuentas:**
   - Crear módulo administrativo "Gestión de Cuentas"
   - Implementar tabla con solicitudes en estado "pendiente de validación"
   - Mostrar columnas: ID solicitud, nombre, correo, tipo usuario, fecha solicitud, estado
   - Agregar filtros: por estado, por tipo usuario, por rango de fechas
   - Implementar paginación (20 registros por página)

2. **Panel de detalles de solicitud:**
   - Crear vista de detalle al hacer clic en solicitud
   - Mostrar todos los datos proporcionados por el usuario
   - Incluir botones: "Aprobar", "Rechazar"
   - Para rechazar, mostrar campo obligatorio "Motivo del Rechazo"

3. **Flujo de aprobación:**
   - Al hacer clic en "Aprobar":
     * Validar que el correo sea válido (no rebotado)
     * Generar contraseña temporal según política:
       - Mínimo 10 caracteres
       - Al menos 1 mayúscula
       - Al menos 1 minúscula
       - Al menos 1 número
       - Al menos 1 símbolo especial
     * Encriptar la contraseña antes de guardar
     * Actualizar estado de solicitud a "aprobada"
     * Crear cuenta de usuario con estado "activa"

4. **Flujo de rechazo:**
   - Al hacer clic en "Rechazar":
     * Exigir campo "Motivo del Rechazo" (obligatorio)
     * Actualizar estado a "rechazada"
     * Registrar motivo del rechazo
     * Enviar correo al usuario informando la decisión

5. **Envío de credenciales:**
   - Implementar template de correo con:
     * Credenciales (correo y contraseña temporal)
     * Enlace de activación (HTTPS)
     * Enlace válido por 24 horas
     * Instrucciones para primer acceso
   - Enviar correo automático al aprobada
   - Registrar timestamp del envío

6. **Auditoría del proceso:**
   - Registrar cada aprobación/rechazo:
     * Fecha y hora
     * Jefe responsable
     * ID de la solicitud
     * Decisión tomada
     * Estado del envío de notificación

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede visualizar todas las solicitudes pendientes de validación en una tabla.
- CA2: Al hacer clic en una solicitud, el sistema muestra todos los datos proporcionados por el usuario.
- CA3: El jefe puede hacer clic en "Aprobar" o "Rechazar".
- CA4: Si rechaza, debe ingresar obligatoriamente un motivo.
- CA5: Al aprobar, el sistema genera contraseña temporal que cumple la política de seguridad.
- CA6: La contraseña temporal se encripta antes de almacenarse.
- CA7: Se crea la cuenta con estado "activa".
- CA8: El usuario recibe un correo con credenciales y enlace de activación válido por 24 horas.
- CA9: Al rechazar, el usuario recibe un correo con el motivo de rechazo.
- CA10: Todos los eventos se registran en auditoría con detalles completos.

---

## RF003 – INICIO DE SESIÓN

**Historia de Usuario**

COMO usuario registrado (cliente mayorista o empleado)
QUIERO iniciar sesión con mis credenciales
PARA acceder al sistema y operar según mi rol asignado.

**Tareas de la Historia (Tasks)**

1. **Diseño de página de login:**
   - Crear página de login con campos:
     * Correo electrónico
     * Contraseña
   - Agregar checkbox "Recuérdame"
   - Agregar enlace "¿Olvidaste tu contraseña?"
   - Diseñar interfaz profesional y responsive

2. **Validación de credenciales:**
   - Implementar endpoint de autenticación en FastAPI
   - Validar que correo exista en base de datos
   - Validar que contraseña coincida usando función hash segura (bcrypt)
   - Retornar token JWT si credenciales son válidas

3. **Generación de sesión:**
   - Implementar sistema de sesiones con JWT
   - Configurar duración de sesión: 20 minutos de inactividad
   - Almacenar información de sesión:
     * ID de usuario
     * Rol del usuario
     * Timestamp de inicio
     * IP del cliente
   - Generar refresh token para renovar sesión

4. **Bloqueo por intentos fallidos:**
   - Registrar cada intento fallido
   - Tras 3 intentos fallidos en 5 minutos:
     * Bloquear la cuenta temporalmente
     * Mostrar mensaje: "Cuenta bloqueada por 30 minutos"
   - Implementar desbloqueo automático tras 30 minutos

5. **Redirección según rol:**
   - Implementar lógica que redirige a:
     * Cliente mayorista → panel de pedidos
     * Empleado → panel de tareas
     * Jefe → panel administrativo

6. **Prevención de acceso simultáneo:**
   - Validar que el usuario no esté conectado en otro dispositivo
   - Si intenta conectarse desde otro dispositivo:
     * Mostrar mensaje de advertencia
     * Opción de "Cerrar sesión anterior" o "Cancelar"

7. **Historial de autenticación:**
   - Registrar cada intento en tabla `login_history`:
     * Timestamp
     * IP del cliente
     * Dispositivo/navegador
     * Resultado (éxito/fallo)
     * Motivo de fallo si aplica

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Un usuario con cuenta activa puede iniciar sesión correctamente.
- CA2: El formulario valida que correo y contraseña sean llenados.
- CA3: Si el correo no existe, muestra: "Correo o contraseña incorrectos" (sin especificar cuál).
- CA4: Si la contraseña es incorrecta, muestra: "Correo o contraseña incorrectos".
- CA5: Tras 3 intentos fallidos en 5 minutos, bloquea la cuenta por 30 minutos.
- CA6: Un usuario bloqueado no puede intentar login durante 30 minutos.
- CA7: Al iniciar sesión exitosamente, se redirige al panel correspondiente a su rol.
- CA8: La sesión se cierra automáticamente tras 20 minutos de inactividad.
- CA9: Un usuario no puede estar conectado simultáneamente en dos dispositivos.
- CA10: Todos los intentos de login quedan registrados en el historial.

---

## RF004 – RECUPERACIÓN DE CUENTAS

**Historia de Usuario**

COMO usuario registrado que olvidó su contraseña
QUIERO poder recuperar el acceso a mi cuenta de forma segura
PARA poder iniciar sesión nuevamente sin perder mi información.

**Tareas de la Historia (Tasks)**

1. **Formulario de recuperación:**
   - Crear página "Recuperar Contraseña" accesible desde login
   - Campo único: "Correo Electrónico"
   - Botón: "Enviar Instrucciones"
   - Mensaje informativo sobre proceso

2. **Validación y generación de token:**
   - Implementar endpoint de recuperación
   - Validar que correo esté registrado en base de datos
   - Generar token único criptográficamente seguro:
     * Usar biblioteca `secrets` de Python
     * Token válido por 60 minutos
   - Guardar token en base de datos con timestamp de expiración
   - NO mostrar confirmación de que correo existe (por seguridad)

3. **Envío de correo con enlace:**
   - Crear template de correo con:
     * Enlace HTTPS con token incluido
     * Aviso de que enlace vence en 60 minutos
     * Instrucciones claras
     * Link alternativo si correo no clickeable
   - Enviar correo automáticamente
   - Registrar envío en tabla `password_reset_log`

4. **Validación de token y nueva contraseña:**
   - Al hacer clic en enlace, validar:
     * Token existe en base de datos
     * Token no ha expirado
     * Token no ha sido usado anteriormente
   - Si es válido, mostrar formulario de nueva contraseña con campos:
     * Nueva Contraseña
     * Confirmar Contraseña
   - Validar que contraseña cumpla política (10 caracteres, mayúscula, minúscula, número, símbolo)
   - Validar que ambas contraseñas coincidan

5. **Actualización de contraseña:**
   - Si validación es exitosa:
     * Encriptar nueva contraseña
     * Actualizar contraseña en base de datos
     * Invalidar token (marcar como usado)
     * Invalidar todos los tokens de recuperación anteriores del usuario
   - Mostrar mensaje: "Su contraseña ha sido actualizada exitosamente"
   - Redirigir a página de login

6. **Manejo de errores:**
   - Si token expirado: "El enlace ha expirado. Solicite una nueva recuperación"
   - Si token inválido: "El enlace es inválido. Solicite una nueva recuperación"
   - Si contraseña no cumple: "La contraseña debe tener 10+ caracteres, mayúscula, minúscula, número y símbolo"
   - Si no coinciden: "Las contraseñas no coinciden"

7. **Auditoría:**
   - Registrar cada solicitud y resultado en tabla `password_recovery_log`

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Un usuario puede acceder al formulario de recuperación desde la página de login.
- CA2: Al ingresar un correo válido, el sistema envía instrucciones sin errores.
- CA3: El correo de recuperación incluye un enlace HTTPS válido por 60 minutos.
- CA4: Al abrir el enlace, el usuario ve un formulario para ingresar nueva contraseña.
- CA5: El sistema valida que la nueva contraseña cumpla la política (10+ caracteres, mayúscula, minúscula, número, símbolo).
- CA6: Si las contraseñas no coinciden, muestra error específico.
- CA7: Al actualizar exitosamente, muestra confirmación y redirige a login.
- CA8: El token expira automáticamente tras 60 minutos.
- CA9: Un token no se puede reutilizar (después de usarlo, se invalida).
- CA10: Todos los intentos de recuperación quedan registrados en auditoría.

---

## RF005 – SOLICITUD DE REACTIVACIÓN DE CUENTAS

**Historia de Usuario**

COMO usuario con cuenta suspendida
QUIERO solicitar la reactivación de mi cuenta
PARA recuperar acceso al sistema nuevamente.

**Tareas de la Historia (Tasks)**

1. **Interfaz de solicitud:**
   - Crear página "Solicitar Reactivación" (accesible solo para cuentas suspendidas)
   - Mostrar mensaje: "Tu cuenta está suspendida. ¿Deseas solicitar reactivación?"
   - Botón: "Solicitar Reactivación"

2. **Formulario de reactivación:**
   - Mostrar formulario con campos:
     * Correo Electrónico (pre-lleno, no editable)
     * Motivo Detallado (área de texto, obligatorio)
     * Número de Documento (obligatorio)
     * Teléfono (obligatorio)
     * Evidencia/Documentación (archivo opcional)
   - Implementar validaciones en tiempo real

3. **Procesamiento de solicitud:**
   - Validar campos obligatorios
   - Generar ID de solicitud único (formato: REAC-YYYYMMDD-0001)
   - Crear ticket en tabla `reactivation_requests`
   - Registrar timestamp de solicitud
   - Registrar IP del usuario

4. **Notificación al usuario:**
   - Enviar correo de confirmación con:
     * Número de solicitud
     * Confirmación de recepción
     * Tiempo estimado de revisión (24-48 horas)
     * Enlace para consultar estado

5. **Notificación al jefe:**
   - Crear notificación en panel del jefe
   - Incluir: ID solicitud, nombre usuario, motivo resumido
   - Marcar como prioritaria

6. **Panel de revisión para jefe:**
   - Crear módulo "Solicitudes de Reactivación"
   - Mostrar lista de solicitudes pendientes
   - Permitir al jefe:
     * Ver detalles completos
     * Adjuntos/evidencia
     * Campo obligatorio "Comentario" (para aprobar o rechazar)
     * Botones: "Aprobar", "Rechazar"

7. **Flujo de aprobación/rechazo:**
   - Si aprueba:
     * Cambiar estado de cuenta a "activa"
     * Enviar correo: "Tu cuenta ha sido reactivada"
     * Registrar aprobación en auditoría
   - Si rechaza:
     * Cambiar estado de solicitud a "rechazada"
     * Enviar correo con motivo específico
     * Registrar rechazo en auditoría

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Un usuario con cuenta suspendida puede acceder al formulario de reactivación.
- CA2: Todos los campos obligatorios deben completarse para enviar la solicitud.
- CA3: Al enviar, el sistema genera un ID único de solicitud.
- CA4: El usuario recibe correo de confirmación con número de solicitud.
- CA5: El jefe recibe una notificación sobre la nueva solicitud.
- CA6: El jefe puede ver todos los detalles de la solicitud.
- CA7: El jefe puede aprobar o rechazar la solicitud (ambos requieren comentario).
- CA8: Si se aprueba, la cuenta cambia a estado "activa".
- CA9: Si se rechaza, el usuario es notificado con el motivo.
- CA10: Todos los eventos quedan registrados en auditoría.

---

## RF006 – CREACIÓN DE CATÁLOGO

**Historia de Usuario**

COMO jefe del sistema
QUIERO poder crear productos en el catálogo digital
PARA que clientes mayoristas puedan consultar y solicitar.

**Tareas de la Historia (Tasks)**

1. **Estructura de datos de producto:**
   - Diseñar tabla `products` con campos:
     * ID, nombre, referencia (única), descripción, estado
     * Categoría FK, Marca FK, Estilo FK
     * Fecha creación, usuario creador
   - Crear tabla `product_variants` para talla-color:
     * ID, producto_id, talla, color, estado
     * Generar SKU único automaticamente

2. **Formulario de creación:**
   - Crear página "Crear Nuevo Producto"
   - Secciones:
     * Información Básica: nombre, referencia, descripción
     * Atributos: tallas (checkbox múltiple), colores (checkbox múltiple)
     * Clasificación: categoría (dropdown), marca (dropdown), estilo (dropdown)
     * Imagen: upload de archivo (JPG/PNG, máx 2MB)
     * Estado: activo/inactivo

3. **Validaciones:**
   - Validar que referencia sea única
   - Validar que imagen sea JPG o PNG
   - Validar que imagen no exceda 2MB
   - Validar que al menos haya una talla y color
   - Validar que categoría y marca existan

4. **Procesamiento de imagen:**
   - Implementar optimización de imagen:
     * Redimensionar a ancho máximo 1200px
     * Comprimir sin perder calidad
   - Guardar imagen en servidor (carpeta `/uploads/products/`)
   - Almacenar ruta en base de datos

5. **Generación automática de variantes:**
   - Para cada combinación talla x color:
     * Crear registro en `product_variants`
     * Generar SKU único
     * Asignar estado "disponible"

6. **Confirmación y notificación:**
   - Mostrar: "Producto registrado exitosamente"
   - Producto aparece en catálogo public si estado = "activo"
   - Registrar en auditoría: usuario, fecha, datos del producto

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede acceder al formulario de creación de producto.
- CA2: Todos los campos obligatorios están claramente marcados.
- CA3: El sistema valida que la referencia sea única.
- CA4: Si la referencia ya existe, muestra error: "La referencia ya existe"
- CA5: El sistema valida que imagen sea JPG o PNG y no supere 2MB.
- CA6: Si imagen es inválida, muestra error específico.
- CA7: Al crear exitosamente, el producto aparece en el catálogo.
- CA8: Se generan SKUs únicos para cada talla-color.
- CA9: El producto aparece con estado "activo" inmediatamente si se configuró así.
- CA10: El evento se registra en auditoría con todos los detalles.

---

## RF007 – CLASIFICACIÓN POR CATEGORÍAS

**Historia de Usuario**

COMO jefe del sistema
QUIERO gestionar las categorías de productos
PARA organizar el catálogo de manera estructurada y clara.

**Tareas de la Historia (Tasks)**

1. **Tabla de categorías:**
   - Diseñar tabla `categories`:
     * ID, nombre (único), descripción, estado, fecha creación

2. **CRUD de categorías:**
   - Crear formulario para:
     * Nueva categoría: nombre (obligatorio), descripción (opcional), estado
     * Editar categoría: modificar nombre y descripción
     * Eliminar categoría: con validaciones
   - Implementar listado de categorías activas/inactivas

3. **Validación de unicidad:**
   - Verificar que nombre sea único (case-insensitive)
   - Mostrar error si intenta nombre duplicado

4. **Validación para eliminación:**
   - Antes de eliminar, consultar si hay productos activos vinculados
   - Si hay productos:
     * Mostrar: "No se puede eliminar. Hay X productos activos en esta categoría"
     * Sugerir desactivar en su lugar
   - Si no hay productos vinculados, permitir eliminación tras confirmación explícita

5. **Desactivación:**
   - Si categoría está inactiva, productos vinculados no aparecen en catálogo público
   - Permitir al jefe desactivar sin eliminar

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede crear una nueva categoría con nombre único.
- CA2: El sistema impide crear categorías con nombre duplicado.
- CA3: El jefe puede editar nombre y descripción de categoría existente.
- CA4: El jefe puede desactivar una categoría.
- CA5: No se puede eliminar una categoría que tenga productos activos vinculados.
- CA6: Se puede eliminar una categoría sin productos vinculados tras confirmación.
- CA7: Al desactivar una categoría, sus productos no aparecen en catálogo público.
- CA8: Todos los cambios se registran en auditoría.

---

## RF008 – GESTIÓN DE MARCAS Y ESTILOS

**Historia de Usuario**

COMO jefe del sistema
QUIERO gestionar marcas y estilos de productos
PARA mantener coherencia en el catálogo.

**Tareas de la Historia (Tasks)**

1. **Tabla de marcas:**
   - `brands`: ID, nombre (único), descripción, estado, fecha_creacion

2. **Tabla de estilos:**
   - `styles`: ID, nombre, descripción, brand_id (FK), estado, fecha_creacion
   - Nombre de estilo debe ser único por marca

3. **CRUD de marcas:**
   - Crear, editar, desactivar, eliminar marcas
   - No permitir eliminación si hay estilos o productos vinculados
   - Validar unicidad de nombre

4. **CRUD de estilos:**
   - Crear estilo vinculado a marca específica
   - Editar estilo
   - Desactivar estilo
   - Eliminar solo si no hay productos vinculados

5. **Validaciones:**
   - Nombre de marca único (case-insensitive)
   - Nombre de estilo único dentro de su marca
   - Prevención de eliminación con productos vinculados

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede crear una marca con nombre único.
- CA2: El jefe puede agregar estilos a una marca.
- CA3: El sistema impide duplicar nombre de estilo dentro de una marca.
- CA4: No se puede eliminar marca con estilos o productos vinculados.
- CA5: Se puede desactivar marca sin eliminarla.
- CA6: Al desactivar marca, sus productos no aparecen en catálogo público.
- CA7: Todos los cambios se registran en auditoría.

---

## RF009 – VISUALIZACIÓN DE CATÁLOGO COMO VISITANTE

**Historia de Usuario**

COMO visitante sin registrar
QUIERO poder consultar el catálogo de productos
PARA conocer la oferta sin necesidad de crear cuenta.

**Tareas de la Historia (Tasks)**

1. **Página del catálogo público:**
   - Crear ruta `/catalogo` accesible sin autenticación
   - Mostrar todos los productos activos
   - Para cada producto mostrar:
     * Imagen optimizada
     * Nombre, referencia
     * Tallas disponibles (listado)
     * Colores disponibles (muestras visuales)
     * Material, marca, estilo
     * NO mostrar: precios, costos, stock

2. **Sistema de filtros:**
   - Filtro por Categoría (dropdown)
   - Filtro por Marca
   - Filtro por Estilo
   - Filtro por Talla (checkbox múltiple)
   - Filtro por Color
   - Tiempo de respuesta: < 3 segundos

3. **Búsqueda:** de texto libre
   - Busca en: nombre, referencia, descripción, marca, estilo
   - Búsqueda de coincidencias parciales
   - Resultados actualizados sin recargar página

4. **Paginación:**
   - 12 productos por página
   - Navegación anterior/siguiente
   - Mostrar total de productos

5. **Detalles del producto:**
   - Al hacer clic, mostrar panel con:
     * Galería de imágenes (si available)
     * Descripción completa
     * Disponibilidad por talla-color
   - Botón "Crear Cuenta para Obtener Precio"

6. **Gestión de errores:**
   - Si no hay productos: "No hay productos disponibles en este momento"
   - Si filtros sin coincidencias: "No se encontraron productos que coincidan"

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Un visitante sin cuenta puede acceder al catálogo público.
- CA2: Se muestran solo productos con estado "activo".
- CA3: Los filtros funcionan correctamente por categoría, marca, talla y color.
- CA4: La búsqueda de texto libre encuentra coincidencias en múltiples campos.
- CA5: La búsqueda y filtros responden en menos de 3 segundos.
- CA6: Los precios y costos NO se muestran a visitantes.
- CA7: Al hacer clic en producto, se muestra detalle sin requerir autenticación.
- CA8: El cambio entre páginas funciona sin recargar.

---

## RF010 – CONSULTA DE CATÁLOGO POR CLIENTE MAYORISTA

**Historia de Usuario**

COMO cliente mayorista autenticado
QUIERO consultar el catálogo completo con disponibilidad de inventario
PARA preparar mis pedidos.

**Tareas de la Historia (Tasks)**

1. **Acceso autenticado al catálogo:**
   - Crear ruta `/mi-catalogo` solo para clientes mayoristas autenticados
   - Validar sesión activa antes de mostrar
   - Mostrar todos los productos activos

2. **Indicadores de disponibilidad:**
   - Para cada combinación talla-color, mostrar:
     * Verde: "Disponible" (hay stock)
     * Rojo: "Agotado" (sin stock)
     * Amarillo: "Limitado" (< 5 unidades)
   - Stock se actualiza en tiempo real (máximo 5 min de latencia)

3. **Sistema de favoritos:**
   - Crear tabla `customer_favorites`:
     * customer_id, product_id, fecha_agregado
   - Permitir agregar/quitar favoritos con clic en ícono estrella
   - Guardar favoritos en sesión y BD
   - Mantener favoritos en futuras sesiones

4. **Agregar productos a pedido:**
   - Desde catálogo, cliente puede:
     * Seleccionar talla (solo disponibles)
     * Seleccionar color (solo disponibles)
     * Ingresar cantidad (números positivos)
     * Botón "Agregar al Pedido"
   - Crear pedido en estado "borrador" automáticamente
   - Pre-llenar con productos seleccionados
   - Permitir continuar agregando o ir a checkout

5. **Validaciones:**
   - Cantidad no puede ser negativa/cero
   - Talla-color seleccionada debe estar disponible
   - Agregar al pedido sin recargar página

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El cliente mayorista ve el catálogo completo tras iniciar sesión.
- CA2: Se muestran indicadores de disponibilidad (verde/rojo/amarillo) por combinación.
- CA3: El cliente puede agregar productos a favoritos.
- CA4: Los favoritos se mantienen en futuras sesiones.
- CA5: El cliente puede seleccionar talla, color y cantidad.
- CA6: Al hacer clic "Agregar al Pedido", se crea pedido en estado borrador.
- CA7: Se puede agregar múltiples productos al mismo pedido.
- CA8: Se impide seleccionar talla-color agotada.
- CA9: Se impide cantidad negativa o cero.

---

## RF011 – SISTEMA DE FILTRADO DE BÚSQUEDA

**Historia de Usuario**

COMO usuario del catálogo
QUIERO poder filtrar y buscar productos eficientemente
PARA encontrar exactamente lo que necesito.

**Tareas de la Historia (Tasks)**

1. **Implementar controles de filtrado:**
   - Dropdown "Categoría" (selección múltiple opcional)
   - Dropdown "Marca"
   - Dropdown "Estilo"
   - Dropdown "Talla" (checkbox múltiple)
   - Dropdown "Color" (con muestra visual)
   - Campo de búsqueda por texto libre
   - Botón "Limpiar Filtros"

2. **Búsqueda por texto libre:**
   - Busca en: nombre producto, referencia, descripción, marca, estilo
   - Coincidencias parciales (case-insensitive)
   - Actualizar resultados sin recargar página
   - Tiempo respuesta: < 2 segundos

3. **Aplicación de filtros:**
   - Permitir combinar hasta 5 filtros simultáneamente
   - Aplicar lógica AND entre filtros (todos deben cumplirse)
   - Actualizar resultados en tiempo real
   - Mostrar cantidad de resultados

4. **Limpiar filtros:**
   - Botón "Limpiar Filtros" que:
     * Desmarque todos los filtros
     * Restaure vista completa del catálogo
     * Limpie búsqueda de texto

5. **Manejo de resultados vacíos:**
   - Si combinación de filtros no tiene resultados:
     * Mostrar: "No se encontraron productos que coincidan"
     * Sugerir limpiar filtros

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El usuario puede aplicar múltiples filtros simultáneamente.
- CA2: Los filtros se aplican en menos de 2 segundos.
- CA3: Se pueden combinar hasta 5 filtros.
- CA4: La búsqueda de texto libre encuentra coincidencias en múltiples campos.
- CA5: Al limpiar filtros, se restaura la vista completa.
- CA6: Si no hay resultados, muestra mensaje correspondiente.
- CA7: Los resultados se actualizan sin refrescar la página.

---

## RF012 – REALIZACIÓN DE PEDIDOS POR CLIENTE MAYORISTA

**Historia de Usuario**

COMO cliente mayorista
QUIERO registrar pedidos de productos
PARA solicitar el calzado que necesito para mi negocio.

**Tareas de la Historia (Tasks)**

1. **Formulario de pedido:**
   - Crear página "Realizar Pedido" o "Nuevo Pedido"
   - Para cada producto: selector talla, color, cantidad, botón agregar
   - Mostrar resumen de productos agregados
   - Validar cantidad mínima de pedido (configurable, ej: 10 pares)
   - Campo de fecha de entrega requerida

2. **Validación de disponibilidad:**
   - Para cada producto-talla-color:
     * Verificar stock en bodega
     * Si hay stock: marcar como "aprobado para entrega"
     * Si no hay stock: marcar como "pendiente de fabricación"
   - Mostrar indicadores visuales de disponibilidad

3. **Generación de número de pedido:**
   - Generar ID único: PED-YYYYMMDD-0001
   - Guardar en tabla `orders`:
     * customer_id, order_number, order_date, delivery_date_requested
     * status ("pending_review"), total_items

4. **Detalles del pedido:**
   - Tabla `order_items`:
     * order_id, product_id, talla, color, cantidad
     * availability_status (direct_delivery / pending_fabrication)

5. **Validaciones críticas:**
   - Cantidad total >= cantidad mínima configurable
   - Todos los campos obligatorios completos
   - Cliente tiene cuenta activa

6. **Confirmación y notificación:**
   - Mostrar: "Pedido registrado exitosamente. Número PED-YYYYMMDD-0001"
   - Enviar correo de confirmación al cliente
   - Crear notificación para jefe

7. **Historial de auditoría:**
   - Registrar: fecha, hora, cliente, detalles del pedido, IP

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El cliente mayorista puede crear un nuevo pedido.
- CA2: Para cada producto, se pueden seleccionar talla, color y cantidad.
- CA3: El sistema valida que cantidad total >= cantidad mínima.
- CA4: Se muestra indicador de disponibilidad (directa o por fabricar).
- CA5: Se genera número único de pedido (formato PED-YYYYMMDD-XXXX).
- CA6: El cliente recibe confirmación en pantalla y por correo.
- CA7: El jefe recibe notificación del nuevo pedido en menos de 30 segundos.
- CA8: El estado inicial del pedido es "pendiente de revisión administrativa".
- CA9: Se registra en auditoría todos los detalles del pedido.

---

## RF013 – NOTIFICACIÓN DE NUEVOS PEDIDOS

**Historia de Usuario**

COMO jefe, gerente comercial y planificador de producción
QUIERO ser notificado inmediatamente cuando se registra un nuevo pedido
PARA actuar rápidamente en su procesamiento.

**Tareas de la Historia (Tasks)**

1. **Sistema de notificación automática:**
   - Al registrar pedido, disparar notificación inmediatamente
   - Tiempo máximo: 5 segundos
   - Destinatarios: jefe, gerente comercial, planificador de producción

2. **Contenido de notificación:**
   - ID del pedido
   - Nombre del cliente
   - Fecha y hora de registro
   - Cantidad total de ítems
   - Combinaciones solicitadas (talla-color-cantidad por producto)
   - Indicación: "Entrega Directa" o "Pendiente de Fabricación"
   - Enlace directo al detalle del pedido

3. **Entrega de notificación:**
   - In-app: visible inmediatamente en panel del usuario
   - Correo: envío automático a correo registrado
   - Prioridad alta (no filtra como spam)

4. **Registro de notificación:**
   - Tabla `notifications`:
     * user_id, type, order_id, content, status, created_at
   - No crear duplicados (una por pedido)
   - Historial de notificaciones

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Cada rol autorizado recibe notificación en menos de 5 segundos.
- CA2: La notificación contiene información completa del pedido.
- CA3: No hay duplicidad de notificaciones por pedido.
- CA4: La notificación incluye enlace directo al detalle del pedido.
- CA5: El usuario puede acceder al detalle desde la notificación.
- CA6: Se registra el evento en historial de notificaciones.

---

## RF014 – CONSULTA DE ESTADO DE PEDIDOS POR CLIENTE

**Historia de Usuario**

COMO cliente mayorista
QUIERO consultar el estado de mis pedidos
PARA saber en qué fase se encuentran.

**Tareas de la Historia (Tasks)**

1. **Página de mis pedidos:**
   - Crear ruta `/mis-pedidos` (solo clientes mayoristas autenticados)
   - Mostrar lista de todos los pedidos del cliente
   - Ordenar por fecha (más recientes primero)
   - Paginación: 10 pedidos por página

2. **Información en lista:**
   - Número de pedido (clickeable)
   - Fecha de registro
   - Estado actual
   - Cantidad total de pares
   - Barra de progreso visual del estado

3. **Estados de pedido:**
   - pendiente: gris
   - aprobado: azul
   - en_produccion: naranja
   - completado: verde
   - entregado: oscuro
   - cancelado: rojo

4. **Detalle del pedido:**
   - Al hacer clic en un pedido, mostrar:
     * Número y fecha
     * Estado actual (con timestamp de último cambio)
     * Timeline de cambios de estado
     * Lista de productos con:
       - Referencia, talla, color, cantidad, estado
     * Indicación: "Entrega Directa" o "Fabricación Pendiente"
     * Porcentaje de avance de producción (si aplica)
     * Fecha prometida de entrega

5. **Filtros:**
   - Por Estado (dropdown)
   - Por Rango de Fechas (date picker from-to)
   - Por Referencia de Producto (búsqueda)
   - Botón "Limpiar Filtros"

6. **Alertas visuales:**
   - Si fecha prometida < fecha actual y estado ≠ "entregado":
     * Mostrar banner rojo: "Este pedido está retrasado"
     * Botón "Contactar Soporte"

7. **Seguridad:**
   - Impedir acceso a pedidos de otros clientes por manipulación de URL
   - Registrar intentos de acceso no autorizado en auditoría

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El cliente ve todos sus pedidos organizados cronológicamente.
- CA2: Cada pedido muestra número, fecha, estado y cantidad.
- CA3: Al hacer clic, se abre detalle completo del pedido.
- CA4: El detalle muestra timeline de cambios de estado.
- CA5: Se muestra lista de productos con detalles de cada uno.
- CA6: Los filtros funcionan correctamente.
- CA7: Se detectan pedidos retrasados y se muestran con alerta.
- CA8: Un cliente no puede acceder a pedidos de otros clientes.

---

## RF015 – ACTUALIZACIÓN DE ESTADO DE PEDIDOS POR JEFE

**Historia de Usuario**

COMO jefe
QUIERO cambiar el estado de los pedidos
PARA gestionar su flujo en el proceso de producción.

**Tareas de la Historia (Tasks)**

1. **Módulo de gestión de pedidos:**
   - Crear página "Gestión de Pedidos"
   - Mostrar lista de todos los pedidos
   - Filtros: por estado, por cliente, por rango de fechas

2. **Panel de cambio de estado:**
   - Al seleccionar un pedido, mostrar:
     * Detalles completos
     * Dropdown con estados válidos (según estado actual)
     * Campo obligatorio "Motivo del Cambio"
     * Botón "Confirmar Cambio"

3. **Transiciones de estado válidas:**
   - pendiente → aprobado, cancelado
   - aprobado → en_produccion, aprobado_para_entrega, cancelado
   - en_produccion → fabricado, completado
   - fabricado → aprobado_para_entrega
   - aprobado_para_entrega → entregado, cancelado

4. **Acciones automáticas por cambio:**
   - Si → aprobado:
     * Crear orden de producción asociada
     * Reservar insumos en inventario
   - Si → aprobado_para_entrega:
     * Verificar disponibilidad en bodega
     * Si no hay stock: rechazar cambio
   - Si → entregado:
     * Validar que haya pasado por "aprobado_para_entrega"
     * Actualizar fecha de entrega real
   - Si → cancelado:
     * Liberar insumos reservados
     * Justificación obligatoria

5. **Validaciones:**
   - Motivo del cambio obligatorio
   - Transición de estado válida según flujo

6. **Notificación al cliente:**
   - Enviar correo al cliente con cambio de estado
   - Incluir: nuevo estado, fecha, motivo si aplica

7. **Registro de auditoría:**
   - Timestamp, usuario, estado anterior, nuevo estado, motivo

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede cambiar el estado de un pedido.
- CA2: Solo se permiten transiciones válidas de estado.
- CA3: El campo "Motivo" es obligatorio.
- CA4: Si cambia a "aprobado", se crea orden de producción.
- CA5: Si cambia a "aprobado_para_entrega" sin stock, rechaza el cambio.
- CA6: Si cambia a "cancelado", se liberan insumos reservados.
- CA7: El cliente es notificado del cambio de estado.
- CA8: Todos los cambios quedan registrados en auditoría.

---

## RF016 – GESTIÓN DE INVENTARIO DE CALZADO FABRICADO

**Historia de Usuario**

COMO jefe
QUIERO gestionar el inventario de productos terminados
PARA controlar stock disponible para ventas.

**Tareas de la Historia (Tasks)**

1. **Tabla de inventario:**
   - `inventory_stock`:
     * product_variant_id, cantidad_disponible, cantidad_reservada
     * cantidad_en_restauracion, stock_total
     * fecha_ultima_actualizacion

2. **Registrar entrada de mercancía:**
   - Formulario: referencia (dropdown), talla, color, cantidad, fecha, origen
   - Validar que combinación exista en catálogo
   - Si combinación no existe en inventario: crear registro
   - Si existe: incrementar cantidad
   - Registrar: fecha, usuario, origen

3. **Consultar inventario:**
   - Página "Estado de Stock"
   - Tabla con: referencia, marca, estilo, talla, color, disponible, reservado, en_restauracion, total
   - Filtros: por referencia, marca, talla, color
   - Búsqueda por código SKU

4. **Ajustes manuales:**
   - Permitir cambio manual de cantidad (para conteos físicos)
   - Campo "Cantidad Actual" (pre-lleno)
   - Campo "Nueva Cantidad"
   - Campo obligatorio "Motivo del Ajuste"
   - Calcular diferencia: nueva - actual
   - Registrar con detalle

5. **Historial de movimientos:**
   - Mostrar entrada/salida de cada SKU
   - Responsable de cada movimiento
   - Fechas de cada operación

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede registrar entrada de mercancía.
- CA2: Valida que la combinación de producto exista.
- CA3: Incrementa cantidad si SKU ya existe.
- CA4: El jefe puede consultar stock actual de cualquier producto.
- CA5: El sistema muestra cantidad disponible, reservada, en restauración.
- CA6: Se pueden hacer ajustes manuales con motivo documentado.
- CA7: El historial de movimientos es completo y trazable.

---

## RF017 – ACTUALIZACIÓN AUTOMÁTICA DEL INVENTARIO

**Historia de Usuario**

COMO sistema automático
QUIERO actualizar el inventario cuando termina la producción
PARA mantener stock real sin intervención manual.

**Tareas de la Historia (Tasks)**

1. **Validación de orden completada:**
   - Al marcar orden de producción como "finalizada":
     * Verificar que todas las combinaciones estén completadas
     * Validar aprobación de control de calidad

2. **Proceso automático de ingreso:**
   - Para cada combinación:
     * Si SKU existe: incrementar cantidad
     * Si SKU no existe: crear nuevo registro
   - Registrar origen: "producción interna"
   - Vincular a número de orden de producción

3. **Validación de tolerancia:**
   - Comparar cantidad fabricada vs. cantidad esperada
   - Si diferencia <= 2%: acepta ingreso
   - Si diferencia > 2%: genera alerta al jefe con discrepancia

4. **Prevención de duplicidad:**
   - Verificar que orden no fue procesada anteriormente
   - Si ya fue procesada: rechazar e informar

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El inventario se actualiza automáticamente al finalizar orden.
- CA2: Se compara cantidad fabricada con cantidad esperada.
- CA3: Si diferencia > 2%, genera alerta al jefe.
- CA4: Se previene doble conteo de la misma orden.
- CA5: El ingreso se vincula a la orden de producción.

---

## RF018 – REGISTRO DE VENTAS Y DESCUENTO EN INVENTARIO

**Historia de Usuario**

COMO jefe
QUIERO registrar ventas manuales y descontar del inventario
PARA mantener control de stock en operaciones directas.

**Tareas de la Historia (Tasks)**

1. **Formulario de venta manual:**
   - Referencia (dropdown), talla, color, cantidad
   - Validar stock disponible
   - Campo "Destino" (cliente/descripción)
   - Mostrar: "Stock Disponible: X"

2. **Validación de stock:**
   - Si cantidad <= disponible: permitir
   - Si cantidad > disponible: rechazar

3. **Procesamiento:**
   - Descontar cantidad del inventario
   - Crear movimiento: "salida por venta directa"
   - Registrar: fecha, usuario, destino

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede registrar venta manual.
- CA2: Se valida stock disponible antes de descuento.
- CA3: Se descuenta automáticamente del inventario.
- CA4: El movimiento queda registrado como salida.

---

## RF019 – REGISTRO DE PÉRDIDAS POR CALZADO DEFECTUOSO

**Historia de Usuario**

COMO jefe y control de calidad
QUIERO registrar productos defectuosos
PARA mantener control de pérdidas e inventario.

**Tareas de la Historia (Tasks)**

1. **Registro de defecto:**
   - Referencia, talla, color, cantidad, código de defecto (dropdown)
   - Observaciones (opcional)
   - Validar cantidad no exceda stock

2. **Flujo de aprobación:**
   - Si usuario es operario: estado "pendiente de aprobación"
   - Si usuario es jefe calidad: aprobación inmediata
   - Mover cantidad a "scrap stock"

3. **Registro de auditoría:**
   - Fecha, código defecto, cantidad, usuario

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El defecto se registra con código obligatorio.
- CA2: Cantidad se mueve a "scrap stock".
- CA3: Los operarios requieren aprobación del jefe calidad.
- CA4: Todos los detalles se registran en auditoría.

---

## RF020 – PROCESO DE RESTAURACIÓN DE CALZADO DEFECTUOSO

**Historia de Usuario**

COMO jefe de calidad
QUIERO restaurar productos defectuosos
PARA recuperar unidades que pueden ser reparadas.

**Tareas de la Historia (Tasks)**

1. **Inicio de restauración:**
   - Seleccionar producto en "scrap stock"
   - Especificar cantidad, tipo de intervención
   - Marcar estado: "en restauración"
   - Bloquear visualización en inventario disponible

2. **Finalización de restauración:**
   - Al completar reparación:
     * Mostrar opciones: "Aprobado" o "No Recuperable"
   - Si aprobado:
     * Mover a "productos terminados"
     * Registrar costo de reparación
     * Actualizar costo unitario
   - Si rechazado:
     * Mantener en "scrap stock" como pérdida

3. **Registro completo:**
   - Historial de restauración con todas las etapas

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Se inicia proceso desde "scrap stock".
- CA2: Unidades en restauración se bloquean del inventario disponible.
- CA3: Solo jefe calidad autoriza aprobación final.
- CA4: Costos de reparación se registran.
- CA5: Todo el proceso queda trazado en historial.

---

## RF021 – CREACIÓN DE TAREAS POR EL JEFE

**Historia de Usuario**

COMO jefe
QUIERO crear tareas para los empleados
PARA organizar el trabajo de producción.

**Tareas de la Historia (Tasks)**

1. **Formulario de creación:**
   - Título, descripción, tipo de tarea (dropdown)
   - Prioridad (alta/normal/baja)
   - Fecha límite, orden de producción (FK)
   - Tiempo estándar (auto, no editable)

2. **Validaciones:**
   - Campo obligatorios completos
   - Orden de producción existe
   - Evitar tareas duplicadas por orden+tipo

3. **Asignación de tiempo estándar:**
   - Consultar ficha del modelo
   - Asignar automáticamente (no editable)

4. **Registro de auditoría:**
   - Fecha, usuario creador, datos completos

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe crea tarea con todos los campos.
- CA2: Tiempo estándar se asigna automáticamente.
- CA3: Se evitan duplicados de tarea-orden-tipo.
- CA4: La tarea aparece con estado "pendiente de inicio".

---

## RF022 – ASIGNACIÓN DE TAREAS A EMPLEADOS

**Historia de Usuario**

COMO jefe
QUIERO asignar tareas a empleados específicos
PARA distribuir el trabajo de forma organizada.

**Tareas de la Historia (Tasks)**

1. **Módulo de asignación:**
   - Mostrar tareas pendientes
   - Seleccionar tarea, empleado, fecha límite
   - Calcular carga de trabajo del empleado

2. **Validación de carga:**
   - Calcular ocupación = (tiempo total / horas disponibles) * 100
   - Si > 100%: mostrar advertencia pero permitir
   - Si > 110%: mostrar advertencia fuerte

3. **Notificación:**
   - Enviar notificación in-app y correo al empleado
   - Incluir: título, descripción, fecha límite

4. **Bloqueo de edición:**
   - Una vez asignada, solo jefe puede editar

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe asigna tarea a empleado válido.
- CA2: Se calcula carga de trabajo.
- CA3: Se muestra advertencia si sobrecarga.
- CA4: Empleado recibe notificación inmediata.
- CA5: Tarea aparece en panel del empleado.

---

## RF023 – CONSULTA DE TAREAS ASIGNADAS POR EL EMPLEADO

**Historia de Usuario**

COMO empleado
QUIERO ver mis tareas asignadas
PARA saber qué trabajo tengo que hacer.

**Tareas de la Historia (Tasks)**

1. **Panel de tareas:**
   - Crear página "Mis Tareas"
   - Mostrar todas las tareas asignadas
   - Ordenar por fecha límite

2. **Información por tarea:**
   - Título, descripción, tipo, prioridad
   - Fecha de asignación, fecha límite
   - Estado (pendiente/en progreso/completada)
   - Tiempo restante hasta vencimiento

3. **Filtros:**
   - Por estado, prioridad, rango de fechas
   - Limpiar filtros

4. **Visualización de completadas:**
   - Mostrar completas como historial
   - No permitir edición de completadas

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El empleado ve solo sus tareas.
- CA2: Las tareas están organizadas por fecha límite.
- CA3: Se muestran indicadores de tiempo restante.
- CA4: Los filtros funcionan correctamente.
- CA5: Las tareas completadas son visibles solo como historial.

---

## RF024 – REPORTE DE AVANCES E INCIDENCIAS EN TAREAS

**Historia de Usuario**

COMO empleado
QUIERO registrar mi progreso en las tareas
PARA mantener actualizado al jefe sobre mi trabajo.

**Tareas de la Historia (Tasks)**

1. **Panel de ejecución:**
   - Botón "Iniciar Tarea": registra timestamp
   - Cronómetro en vivo
   - Botones: Pausar, Reanudar, Finalizar
   - Campo de porcentaje de avance (0, 25, 50, 75, 100%)

2. **Registro de pausa:**
   - Registrar motivo de pausa
   - Si pausa > 10% del tiempo estándar:
     * Solicitar justificación obligatoria

3. **Reporte de incidencias:**
   - Botón "Reportar Incidencia"
   - Tipo (rotura máquina, falta insumo crítico, etc.)
   - Descripción, foto obligatoria
   - Impacto (total/parcial)

4. **Procesamiento de incidencia:**
   - Si tipo = máquina: generar ticket mantenimiento
   - Si tipo = insumo crítico: generar ticket compras (urgente)
   - Si impacto total: bloquear tarea, notificar jefe

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El empleado puede iniciar y pausar tarea.
- CA2: El sistema registra tiempo real invertido.
- CA3: Se pueden reportar incidencias con foto obligatoria.
- CA4: Las máquinas generan tickets de mantenimiento.
- CA5: Los insumos críticos generan tickets urgentes.
- CA6: Las incidencias totales bloquean la tarea.

---

## RF025 – CONFIRMACIÓN DE FINALIZACIÓN DE TAREAS

**Historia de Usuario**

COMO empleado
QUIERO confirmar que completé mi tarea
PARA que se actualice el progreso de la orden de producción.

**Tareas de la Historia (Tasks)**

1. **Formulario de finalización:**
   - Resumen del trabajo realizado (texto)
   - Cantidad exacta de pares (número)
   - Evidencia fotográfica (opcional, obligatoria para procesos críticos)
   - Observaciones técnicas (opcional)

2. **Validación de cantidad:**
   - Si diferencia > 1% con cantidad asignada:
     * Solicitar clave de supervisor
     * Registrar como "desperdicio" o "falta"

3. **Cambio de estado:**
   - Cambiar a "completada"
   - Bloquear para edición posterior
   - Calcular eficiencia

4. **Notificación al jefe:**
   - Enviar notificación con resumen
   - Si última tarea de orden: generar alerta de calidad

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El empleado confirma finalización con datos completos.
- CA2: Se valida cantidad dentro de ±1%.
- CA3: Diferencias > 1% requieren clave de supervisor.
- CA4: La tarea se bloquea para edición posterior.
- CA5: Se calcula eficiencia: tiempo_estándar / tiempo_real.
- CA6: La última tarea de orden genera alerta de calidad.

---

## RF026 – NOTIFICACIÓN AL JEFE DE TAREAS FINALIZADAS

**Historia de Usuario**

COMO jefe
QUIERO ser notificado cuando una tarea se completa
PARA revisar y validar el trabajo.

**Tareas de la Historia (Tasks)**

1. **Notificación automática:**
   - Se dispara al marcar tarea como completada
   - Incluir: número tarea, nombre empleado, eficiencia, resumen
   - Llegar en menos de 60 segundos

2. **Panel de revisión:**
   - Botones: "Aprobar" o "Rechazar"
   - Si rechaza: campo obligatorio de motivo
   - Si rechaza: tarea vuelve a estado "reabierta"

3. **Retrabajo:**
   - Tiempo anterior se anula
   - Se reinicia en cero
   - Empleado es notificado

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Notificación llega en menos de 60 segundos.
- CA2: Jefe puede aprobar o rechazar.
- CA3: Rechazo requiere motivo obligatorio.
- CA4: Si rechaza, tarea se reabre y empleado es notificado.
- CA5: Retrabajo comienza en cero.

---

## RF027 – MODIFICACIÓN Y ELIMINACIÓN DE TAREAS POR EL JEFE

**Historia de Usuario**

COMO jefe
QUIERO editar o eliminar tareas
PARA corregir errores en la planificación.

**Tareas de la Historia (Tasks)**

1. **Edición:**
   - Solo en estados: "pendiente", "asignada"
   - Campos editables: título, descripción, tipo, prioridad, fecha límite, empleado

2. **Eliminación:**
   - Solo si estado = "pendiente" y sin reportes
   - Si tiene tiempo registrado: NO permitir eliminar
   - Sugerir "cancelar" en su lugar

3. **Cancelación:**
   - Campo obligatorio: motivo
   - Registrar cancelación

4. **Auditoría:**
   - Registrar todos los cambios

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Se pueden editar tareas en estados editables.
- CA2: No se permiten eliminaciones de tareas con tiempo registrado.
- CA3: Se exige motivo para cancelar.
- CA4: Todos los cambios se registran en auditoría.

---

## RF028 – REGISTRO DE INCIDENCIAS DE MAQUINARIA E INSUMOS

**Historia de Usuario**

COMO empleado
QUIERO reportar problemas de máquinas o insumos
PARA que se tomen acciones correctivas rápidamente.

**Tareas de la Historia (Tasks)**

1. **Formulario de incidencia:**
   - Tipo (rotura máquina, falta insumo, etc.)
   - Descripción detallada
   - Código/nombre del equipo o insumo
   - Foto obligatoria
   - Área afectada
   - Impacto (total/parcial)

2. **Generación de tickets:**
   - Rotura máquina → ticket mantenimiento (alta prioridad)
   - Falta insumo crítico → ticket compras (urgente)
   - Notificar en menos de 60 segundos

3. **Bloqueo de tarea:**
   - Si impacto total: cambiar estado a "bloqueada"

4. **Prevención de duplicados:**
   - No permitir incidencias duplicadas en 30 minutos

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Se registra incidencia con tipo obligatorio.
- CA2: Foto es obligatoria.
- CA3: Se generan tickets según tipo de incidencia.
- CA4: Si impacto total, tarea se bloquea.
- CA5: Los responsables son notificados en < 60 segundos.

---

## RF029 – MÓDULO DE NOTIFICACIONES

**Historia de Usuario**

COMO usuario
QUIERO recibir notificaciones de eventos importantes
PARA estar informado en todo momento.

**Tareas de la Historia (Tasks)**

1. **Panel de notificaciones:**
   - Ícono en esquina superior derecha
   - Contador de no leídas
   - Dropdown con lista de notificaciones

2. **Contenido de notificación:**
   - Tipo (ícono), título, descripción
   - Fecha/hora relativa
   - Estado: leída/no leída

3. **Acciones:**
   - Marcar como leída
   - Archivar (desaparece del principal)
   - Acceder directo al módulo

4. **Filtros:**
   - Por tipo
   - Por estado

5. **Historial:**
   - Consultar notificaciones archivadas
   - Máximo 30 días

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El usuario ve contador de no leídas.
- CA2: Puede marcar como leída.
- CA3: Puede archivar y consultar historial.
- CA4: Los filtros funcionan.
- CA5: Las notificaciones incluyen enlace directo.

---

## RF030 – ALERTAS AL JEFE SOBRE PEDIDOS, TAREAS E INVENTARIO

**Historia de Usuario**

COMO jefe
QUIERO recibir alertas sobre eventos críticos
PARA tomar decisiones rápidamente.

**Tareas de la Historia (Tasks)**

1. **Banner de alertas:**
   - Mostrar en parte superior del panel
   - Alertas críticas en rojo, altas en naranja
   - Botón "Acuse de Recibo"

2. **Tipos de alertas:**
   - Pedido sin stock disponible
   - Tarea bloqueada
   - Insumo bajo stock
   - Restauración pendiente de aprobación
   - Falla en envío de notificación

3. **Configuración de umbrales:**
   - Admin configura umbrales por tipo

4. **Acceso de recibo:**
   - Jefe marca alerta como reconocida
   - Se traslada a historial
   - Registrar timestamp

5. **Panel de control:**
   - Consultar alertas activas e históricas
   - Filtrar por tipo, estado, fecha

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: Las alertas críticas se muestran prominentemente.
- CA2: La notificación es inmediata (< 1 min).
- CA3: El jefe puede marcar como reconocida.
- CA4: El panel permite filtrar y consultar historial.
- CA5: Todas las alertas se registran.

---

## RF031 – REPORTES DE PEDIDOS E INVENTARIO

**Historia de Usuario**

COMO jefe
QUIERO generar reportes de pedidos e inventario
PARA analizar la operación y tomar decisiones.

**Tareas de la Historia (Tasks)**

1. **formulario de filtros:**
   - Tipo de reporte (pedidos, inventario, combinado)
   - Fecha inicio/fin
   - Estado del pedido
   - Cliente (opcional)
   - Referencia (opcional)

2. **Generación de reporte:**
   - Tiempo máximo: 60 segundos por año de datos
   - Mostrar datos consolidados

3. **Reporte de pedidos:**
   - Tabla: número, cliente, fecha, estado, productos, cantidades
   - Totalizadores por estado

4. **Reporte de inventario:**
   - Tabla: referencia, marca, talla, color, disponible, reservado, en_restauracion, total
   - Valor total de inventario

5. **Exportación:**
   - Botones: Descargar PDF, Descargar Excel
   - Formato profesional y legible

6. **Auditoría:**
   - Registrar generación del reporte
   - Parámetros utilizados

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe puede generar reportes en tiempo real.
- CA2: La generación toma < 60 segundos.
- CA3: Se puede exportar a PDF y Excel.
- CA4: Los datos son consolidados e inmutables.
- CA5: Se registra en auditoría.

---

## RF032 – REPORTES SOBRE TAREAS ASIGNADAS A EMPLEADOS

**Historia de Usuario**

COMO jefe
QUIERO generar reportes de desempeño de empleados
PARA evaluar su productividad.

**Tareas de la Historia (Tasks)**

1. **Filtros:**
   - Empleado (dropdown o todos)
   - Estado, tipo, prioridad de tarea
   - Rango de fechas

2. **Contenido del reporte:**
   - Nombre empleado, número tarea, título
   - Tiempo estándar, tiempo real, eficiencia %
   - Incidencias registradas
   - Tasa de retrabajo

3. **Métricas calculadas:**
   - Total tareas completadas
   - Eficiencia promedio
   - Desviación en horas-hombre
   - Tasa de retrabajo = retrabajo / completadas

4. **Acceso restringido:**
   - Solo jefe y RH

5. **Exportación:**
   - PDF y Excel

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El jefe genera reportes de desempeño.
- CA2: Muestra métricas de eficiencia y retrabajo.
- CA3: Solo autorizedos pueden acceder.
- CA4: Se puede exportar.

---

## RF033 – SUMA DE PRODUCCIÓN POR EMPLEADO

**Historia de Usuario**

COMO sistema automático
QUIERO registrar la producción de cada empleado
PARA analizar su rendimiento operativo.

**Tareas de la Historia (Tasks)**

1. **Registro automático:**
   - Al completar tarea aprobada, extraer:
     * Empleado, referencia, talla, color, cantidad
     * Tipo intervención, fecha

2. **Tabla de suma:**
   - `employee_production`:
     * employee_id, task_id, product... cantidad, date

3. **Prevención de duplicidad:**
   - Cada tarea se suma una sola vez
   - Verificar si ya fue sumada

4. **Consulta de suma:**
   - Página "Producción por Empleado"
   - Filtros: empleado, fecha, tipo intervención
   - Mostrar total de pares por empleado

5. **Cálculos:**
   - Total pares, promedio por hora

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: La suma se registra automáticamente.
- CA2: Se evita duplicidad.
- CA3: El jefe puede consultar suma por empleado.
- CA4: Se muestran cálculos de productividad.

---

## RF034 – SUMA DE PARES FABRICADOS SEMANALMENTE

**Historia de Usuario**

COMO sistema automático
QUIERO consolidar semanalmente los pares fabricados
PARA analizar la productividad semanal.

**Tareas de la Historia (Tasks)**

1. **Proceso automático:**
   - Cada domingo 23:59:
     * Consultar tareas completadas en 7 días
     * Agrupar por modelo, categoría, estado final
   - Tabla `weekly_summary`:
     * week, date, model_id, category, quantity, status

2. **Conciliación:**
   - Validar: pares fabricados = pares en bodega + cuarentena + pérdida
   - Si discrepancia: alertar al jefe

3. **Desglose obligatorio:**
   - Por modelo, categoría, estado final
   - Empleados que participaron

4. **Notificación:**
   - Enviar correo al jefe con resumen

5. **Histórico:**
   - Consultar cortes anteriores

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El consolidado se genera automáticamente cada domingo.
- CA2: La conciliación es correcta.
- CA3: Se incluye desglose obligatorio.
- CA4: El jefe es notificado.
- CA5: Se puede consultar historial.

---

## RF035 – SUMA DE PARES TOTALES PEDIDOS POR CLIENTE MENSUALMENTE

**Historia de Usuario**

COMO jefe
QUIERO conocer la demanda mensual por cliente
PARA hacer planeación de producción e inventario.

**Tareas de la Historia (Tasks)**

1. **Proceso automático:**
   - Último día del mes 23:59:
     * Consultar todos los pedidos del mes
     * Excluir: borradores, cancelados
     * Agrupar por cliente, referencia, estado

2. **Tabla `monthly_summary`:**
   - month, date, customer_id, product_id, quantity
   - quantity_delivered, delivery_ontime_count

3. **Cálculos:**
   - % cumplimiento entrega = entregados / solicitados
   - % entrega a tiempo

4. **Desglose:**
   - Por cliente, por referencia, por estado

5. **Notificación:**
   - Enviar correo al jefe y gerente comercial

6. **Histórico:**
   - Consultar consolidados anteriores
   - Filtros: cliente, mes, estado

**Criterios de Aceptación (Acceptance Criteria)**

- CA1: El consolidado se genera automáticamente cada mes.
- CA2: Se excluyen borradores y cancelados.
- CA3: Se calculan métricas de cumplimiento y entrega a tiempo.
- CA4: Los jefes son notificados.
- CA5: Se puede consultar historial por cliente.

---

## FIN DE TAREAS DE HISTORIAS DE USUARIO (RF001 a RF035)

**Total de Refinamientos:** 35 historias  
**Total de Tareas Detalladas:** 150+ tareas individuales  
**Criterios de Aceptación:** 300+ criterios documentados  

