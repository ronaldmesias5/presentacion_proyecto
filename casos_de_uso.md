# CASOS DE USO
## Sistema de Gestión y Producción de Calzado

---

## CU-001: CREACIÓN DE CUENTAS DE ACCESO
**Actor(es):** Cliente Potencial, Sistema
**Descripción:** Permite al cliente potencial crear una solicitud de cuenta de acceso de forma autónoma desde la página principal, sin intervención del jefe en la fase inicial.

**Precondiciones:**
1. La página principal del sistema está disponible y operativa
2. El módulo de registro público es accesible sin autenticación
3. La base de datos y servicios de validación están funcionando

**Flujo Principal:**
1. El cliente potencial accede a la página principal del sistema
2. Selecciona el botón "Crear Cuenta" o "Registrarse"
3. El sistema presenta un formulario con campos organizados en secciones:
   - Datos Personales: nombre completo, documento de identidad, teléfono, dirección, ciudad
   - Datos Comerciales: razón social, NIT
   - Datos de Cuenta: correo electrónico, tipo de usuario (cliente mayorista)
4. El cliente completa todos los campos obligatorios
5. El cliente hace clic en "Validar y Crear Solicitud"
6. El sistema ejecuta validaciones en tiempo real:
   - Verifica formato válido de correo electrónico
   - Valida que el documento de identidad tenga el formato correcto
   - Valida que el NIT sea válido
   - Consulta en base de datos por duplicados de correo, documento y NIT
7. Si no hay duplicados, el sistema crea un registro con estado "pendiente de validación"
8. El sistema genera un identificador único de solicitud
9. El sistema envía un correo automático al cliente que incluye:
   - Confirmación de recepción de solicitud
   - Número de solicitud
   - Instrucciones para el proceso de validación
   - Enlace para consultar estado
10. El sistema muestra mensaje: "Solicitud enviada exitosamente. Se han enviado los detalles al correo."
11. El cliente es redirigido a página de estado de solicitud

**Flujos Alternos:**
- **Correo Duplicado:** Si el correo ya existe, muestra: "El correo electrónico ya está registrado. Utilice otro correo o recupere la cuenta existente."
- **Documento Duplicado:** Si el documento ya existe, muestra: "El documento de identidad ya existe en el sistema. Verifique la información."
- **NIT Duplicado:** Si el NIT ya existe, muestra: "El NIT ya está registrado. Verifique la información."
- **Formato Inválido:** Si algún campo tiene formato incorrecto, muestra mensaje específico: "Formato de correo inválido" o "Formato de documento incorrecto"
- **Campos Incompletos:** Si faltan campos obligatorios, muestra: "Complete todos los campos obligatorios" y destaca los vacíos en rojo

**Postcondiciones:**
1. El nuevo registro queda en base de datos con estado "pendiente de validación"
2. El correo de confirmación ha sido enviado al cliente
3. Se genera registro de auditoría con: timestamp, IP del cliente, datos de la solicitud
4. El jefe recibe una notificación sobre la nueva solicitud pendiente
5. El cliente puede consultar el estado de su solicitud con su número de identificación

---

## CU-002: VALIDACIÓN Y ACTIVACIÓN DE CUENTAS
**Actor(es):** Jefe del Sistema
**Descripción:** Permite al jefe revisar, validar y activar las solicitudes de cuentas pendientes, controlando el acceso y garantizando la seguridad del sistema.

**Precondiciones:**
1. El jefe ha iniciado sesión con privilegios de administrador
2. Existen solicitudes pendientes de validación en el sistema
3. El módulo de gestión de usuarios y validación está disponible

**Flujo Principal:**
1. El jefe navega al módulo "Gestión de Usuarios" → "Validar Cuentas Pendientes"
2. El sistema presenta una lista de solicitudes con estado "pendiente de validación"
3. Cada solicitud muestra: número, nombre, correo, documento, fecha de solicitud, razón social
4. El jefe selecciona una solicitud para revisar
5. El sistema abre panel de detalle con todos los datos proporcionados
6. El jefe revisa la información completamente
7. El jefe selecciona "Aprobar" o "Rechazar"
8. Si selecciona "Aprobar":
   - El jefe confirma la acción
   - El sistema genera contraseña temporal: mínimo 10 caracteres, mayúscula, minúscula, número y símbolo
   - El sistema encripta la contraseña
   - El sistema crea la cuenta con estado "activa"
   - El sistema envía correo al cliente que incluye:
     * Credenciales (correo y contraseña temporal)
     * Enlace de activación válido por 24 horas
     * Instrucciones para primer acceso
   - El sistema registra: fecha, hora, jefe responsable, acción, resultado
   - El sistema muestra: "Cuenta activada y notificada correctamente"
9. Si selecciona "Rechazar":
   - El formulario exige un comentario de justificación
   - El sistema registra el rechazo
   - El sistema envía correo al cliente informando la decisión y motivo
   - El sistema cambia estado a "rechazada"
   - El sistema muestra: "Solicitud rechazada. Notificación enviada al cliente"

**Flujos Alternos:**
- **Correo No Válido:** Si el sistema detecta que el correo es inválido (rebotado), muestra: "El correo electrónico no es válido. Solicite al cliente que registre un correo activo."
- **Error en Envío de Correo:** Si el correo no se envía, muestra: "Error al enviar credenciales. La cuenta está aprobada pero pendiente de notificación. Reintente el envío."
- **Solicitud Duplicada:** Si encuentra múltiples solicitudes del mismo cliente, muestra: "Existen múltiples solicitudes para este cliente. Revise antes de proceder."

**Postcondiciones:**
1. Si se aprobó: 
   - La cuenta está creada y activa en la base de datos
   - El cliente ha recibido sus credenciales por correo
   - Se genera registro de auditoría con detalles de la aprobación
2. Si se rechazó:
   - La solicitud está marcada como rechazada
   - El cliente fue notificado del rechazo
   - Se genera registro de auditoría con motivo del rechazo

---

## CU-003: INICIO DE SESIÓN
**Actor(es):** Usuario del Sistema
**Descripción:** Permite al usuario autenticado acceder al sistema usando sus credenciales personales.

**Precondiciones:**
1. El usuario tiene una cuenta creada y activa en el sistema
2. La página de login está disponible
3. El servicio de autenticación está operativo

**Flujo Principal:**
1. El usuario accede a la página de login del sistema
2. El sistema presenta formulario con campos: correo electrónico y contraseña
3. El usuario ingresa su correo electrónico
4. El usuario ingresa su contraseña
5. El usuario hace clic en "Iniciar Sesión"
6. El sistema valida que ambos campos estén completos
7. El sistema consulta base de datos por cuenta con ese correo
8. Si existe, el sistema valida que la contraseña coincida usando hashing seguro
9. Si es correcta:
   - El sistema inicia sesión controlada temporalmente
   - El sistema registra: timestamp, IP, intento exitoso
   - El sistema redirige al usuario al panel correspondiente a su rol
   - El sistema muestra bienvenida con nombre del usuario
10. Si es incorrecta o el correo no existe:
    - El sistema registra el intento fallido
    - El sistema cuenta intentos fallidos en los últimos 5 minutos
    - El sistema muestra: "Correo o contraseña incorrectos"

**Flujos Alternos:**
- **Campos Vacíos:** Si falta correo o contraseña, muestra: "Complete todos los campos"
- **Cuenta No Existe:** Si el correo no está registrado, muestra: "El correo no está registrado en el sistema"
- **Cuenta Suspendida:** Si la cuenta está suspendida, muestra: "Tu cuenta está suspendida. Solicita reactivación"
- **Tres Intentos Fallidos:** Tras 3 intentos fallidos en 5 minutos, muestra: "Cuenta bloqueada por 30 minutos por seguridad"
- **Inactividad en Sesión Previa:** Si la sesión previa no se cerró correctamente, detecta y permite nuevo login

**Postcondiciones:**
1. El usuario tiene sesión activa en el sistema
2. La sesión tiene duración máxima de 20 minutos de inactividad
3. Se registra el acceso en auditoría
4. El usuario no puede acceder simultáneamente desde múltiples dispositivos
5. El contador de intentos fallidos se reinicia

---

## CU-004: RECUPERACIÓN DE CUENTAS
**Actor(es):** Usuario del Sistema
**Descripción:** Permite al usuario que olvidó su contraseña recuperar el acceso de forma segura.

**Precondiciones:**
1. El usuario tiene una cuenta creada y activa
2. El formulario de recuperación está disponible en la página de login
3. El servicio de correo está operativo

**Flujo Principal:**
1. El usuario en la página de login selecciona "¿Olvidaste tu contraseña?"
2. El sistema presenta formulario con campo "Correo Electrónico"
3. El usuario ingresa su correo
4. El usuario hace clic en "Recuperar Contraseña"
5. El sistema consulta base de datos por cuenta con ese correo
6. Si existe:
   - El sistema genera token único criptográficamente seguro
   - El sistema asigna vigencia de 60 minutos al token
   - El sistema crea enlace seguro HTTPS con el token
   - El sistema envía correo al usuario que incluye:
     * Enlace de recuperación
     * Instrucciones
     * Advertencia: el enlace vence en 60 minutos
   - El sistema muestra: "Se han enviado instrucciones a tu correo"
7. El usuario abre el correo y hace clic en el enlace
8. El sistema valida que el token sea válido y no haya expirado
9. Si es válido:
   - El sistema presenta formulario de nueva contraseña
   - El usuario ingresa nueva contraseña
   - El usuario confirma la contraseña (escribir dos veces)
   - El usuario hace clic en "Actualizar Contraseña"
10. El sistema valida que la contraseña cumpla requisitos:
    - Mínimo 10 caracteres
    - Al menos una mayúscula
    - Al menos una minúscula
    - Al menos un número
    - Al menos un símbolo especial
11. Si cumple:
    - El sistema encripta la contraseña nueva
    - El sistema reemplaza la contraseña anterior
    - El sistema invalida todos los tokens de recuperación anteriores
    - El sistema registra: fecha, hora, IP
    - El sistema muestra: "Su contraseña ha sido actualizada exitosamente. Ya puede iniciar sesión."
12. El usuario es redirigido a página de login

**Flujos Alternos:**
- **Correo No Existe:** Si el correo no está registrado, muestra: "El correo no está registrado en el sistema"
- **Token Expirado:** Si el usuario abre el enlace después de 60 minutos, muestra: "El enlace ha expirado. Solicite una nueva recuperación"
- **Token Inválido:** Si el token fue manipulado, muestra: "El enlace es inválido. Solicite una nueva recuperación"
- **Contraseña No Cumple Requisitos:** Si la contraseña no cumple, muestra: "La contraseña debe tener 10+ caracteres, mayúscula, minúscula, número y símbolo"
- **Contraseñas No Coinciden:** Si confirmación no coincide con la primera, muestra: "Las contraseñas no coinciden"
- **Contraseña Anterior:** Si intenta usar contraseña anterior, muestra: "No puede usar la contraseña anterior"

**Postcondiciones:**
1. La contraseña anterior queda invalidada
2. La contraseña nueva está en vigor
3. Se registra el evento en auditoría
4. El usuario puede iniciar sesión con las nuevas credenciales
5. Todos los tokens de recuperación anteriores son inválidos

---

## CU-005: SOLICITUD DE REACTIVACIÓN DE CUENTAS
**Actor(es):** Usuario con Cuenta Suspendida
**Descripción:** Permite al usuario cuya cuenta está suspendida solicitar su reactivación.

**Precondiciones:**
1. El usuario tiene una cuenta con estado "suspendida" o "inactiva"
2. El formulario de reactivación está disponible
3. El servicio de tickets está operativo

**Flujo Principal:**
1. El usuario intenta acceder a login pero su cuenta está suspendida
2. El sistema presenta mensaje: "Tu cuenta está suspendida. ¿Deseas solicitar reactivación?"
3. El usuario selecciona "Solicitar Reactivación"
4. El sistema presenta formulario con campos:
   - Correo Electrónico (pre-lleno, no editable)
   - Motivo Detallado (campo de texto obligatorio)
   - Número de Documento (obligatorio)
   - Teléfono (obligatorio)
   - Evidencia/Documentación (archivo opcional)
5. El usuario completa el formulario detalladamente
6. El usuario hace clic en "Enviar Solicitud"
7. El sistema valida que los campos obligatorios estén completos
8. Si es válido:
   - El sistema genera ticket con ID único (ej: REAVCT-20260301-001)
   - El sistema registra: fecha, hora, usuario, información proporcionada
   - El sistema envía correo de confirmación al usuario incluyendo:
     * Número de solicitud
     * Confirmación de recepción
     * Tiempo estimado de revisión
   - El sistema envía notificación al jefe sobre nueva solicitud de reactivación
   - El sistema muestra: "Solicitud enviada correctamente. Tu número de solicitud es: REAVCT-20260301-001"

**Flujo - Revisión por Jefe:**
1. El jefe accede a módulo de solicitudes pendientes
2. El jefe selecciona una solicitud de reactivación
3. El jefe revisa toda la información proporcionada
4. El jefe selecciona "Aprobar" o "Rechazar" (ambas requieren comentario obligatorio)
5. Si aprueba:
   - El sistema cambia estado de cuenta a "activa"
   - El sistema envía correo al usuario: "Tu cuenta ha sido reactivada. Puedes acceder nuevamente"
   - El sistema registra aprobación
6. Si rechaza:
   - El sistema registra rechazo con motivo
   - El sistema envía correo al usuario con motivo del rechazo
   - El sistema registra que la solicitud fue rechazada

**Flujos Alternos:**
- **Campos Incompletos:** Si falta información obligatoria, muestra: "Complete todos los campos requeridos"
- **Formulario Vacío:** Si intenta enviar sin información, muestra: "Es obligatorio ingresar un motivo"

**Postcondiciones:**
1. Un ticket de reactivación está en el sistema con estado "pendiente"
2. El jefe ha sido notificado
3. El usuario ha recibido confirmación por correo
4. Se registra en auditoría la solicitud completa

---

## CU-006: CREACIÓN DE CATÁLOGO
**Actor(es):** Jefe, Diseñador de Producto
**Descripción:** Permite registrar nuevos productos en el catálogo digital del sistema.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o diseñador
2. El módulo de catálogo está disponible
3. La base de datos de productos está operativa
4. Existen categorías y marcas previamente registradas

**Flujo Principal:**
1. El jefe navega a "Catálogo" → "Crear Nuevo Producto"
2. El sistema presenta formulario de registro con secciones:
   - **Información Básica:** nombre, referencia, descripción, estado (activo/inactivo)
   - **Atributos:** tallas disponibles, colores disponibles, material
   - **Clasificación:** categoría (dropdown), marca (dropdown)
   - **Imagen:** cargar archivo JPG o PNG
3. El usuario completa todos los campos obligatorios
4. El usuario carga una imagen del producto (JPG o PNG, máximo 2MB)
5. El usuario hace clic en "Validar y Crear"
6. El sistema ejecuta validaciones:
   - Verifica que la referencia sea única (no existe otra con ese code)
   - Valida que la imagen sea JPG o PNG
   - Valida que la imagen no exceda 2MB
   - Valida que la categoría exista y esté activa
   - Valida que la marca exista y esté activa
   - Valida que al menos se haya seleccionado una talla y un color
7. Si todas las validaciones pasan:
   - El sistema crea el producto en base de datos
   - El sistema genera SKUs únicos para cada combinación talla-color-modelo
   - El sistema almacena la imagen optimizada
   - El sistema registra: fecha, hora, usuario creador
   - El sistema muestra: "Producto registrado exitosamente"
   - El producto aparece en el catálogo con estado "activo" si así se configuró

**Flujos Alternos:**
- **Referencia Duplicada:** Si ya existe una referencia igual, muestra: "La referencia ya existe. Use otra referencia única"
- **Imagen Invalida:** Si la imagen no es JPG/PNG o supera 2MB, muestra: "Imagen debe ser JPG o PNG y no superar 2MB"
- **Categoría No Existe:** Si la categoría seleccionada no existe, muestra: "Categoría no válida"
- **Marca No Existe:** Si la marca seleccionada no existe, muestra: "Marca no válida"
- **Campos Obligatorios Vacíos:** Si faltan campos obligatorios, muestra: "Complete todos los campos obligatorios"

**Postcondiciones:**
1. El producto existe en base de datos con estado especificado
2. Se han generado SKUs para cada combinación talla-color
3. La imagen está almacenada en el servidor
4. Si está activo, aparece en el catálogo público
5. Se registra en auditoría: usuario creador, timestamp, datos completos

---

## CU-007: CLASIFICACIÓN POR CATEGORÍAS
**Actor(es):** Jefe
**Descripción:** Permite gestionar categorías de productos en el catálogo digital.

**Precondiciones:**
1. El jefe ha iniciado sesión
2. El módulo de gestión de categorías está disponible
3. La base de datos de categorías está operativa

**Flujo Principal - Crear Categoría:**
1. El jefe navega a "Catálogo" → "Gestión de Categorías"
2. El sistema presenta lista de categorías existentes
3. El jefe selecciona "Crear Nueva Categoría"
4. El sistema presenta formulario con campos:
   - Nombre (obligatorio, único)
   - Descripción (opcional)
   - Estado (activo/inactivo)
5. El jefe ingresa nombre único sin caracteres especiales
6. El jefe hace clic en "Crear"
7. El sistema valida que el nombre sea único
8. Si es válido:
   - El sistema crea la categoría
   - El sistema muestra: "Categoría creada exitosamente"
   - La categoría aparece en la lista

**Flujo Principal - Editar Categoría:**
1. El jefe abre lista de categorías existentes
2. El jefe selecciona una categoría para editar
3. El jefe modifica nombre o descripción
4. El jefe hace clic en "Guardar Cambios"
5. El sistema valida unicidad del nuevo nombre
6. Si es válido, guarda cambios y muestra confirmación

**Flujo Principal - Eliminar Categoría:**
1. El jefe selecciona una categoría
2. El jefe hace clic en "Eliminar"
3. El sistema verifica si hay productos active vinculados
4. Si NO hay productos activos:
   - El sistema pide confirmación
   - El jefe confirma
   - El sistema elimina la categoría
   - El sistema muestra: "Categoría eliminada"
5. Si HAY productos activos:
   - El sistema muestra: "No se puede eliminar. Hay X productos activos en esta categoría"
   - El sistema sugiere reasignar productos o desactivarla

**Flujos Alternos:**
- **Nombre Duplicado:** Si el nombre ya existe, muestra: "El nombre de categoría ya existe"
- **Caracteres Especiales:** Si tiene caracteres no permitidos, muestra: "No use caracteres especiales"
- **En Uso:** Si intenta eliminar con productos vinculados, muestra: "Reasigne o desactive productos antes"

**Postcondiciones:**
1. Si se creó: la categoría existe y está disponible para asignar productos
2. Si se editó: los cambios están reflejados en el sistema
3. Si se eliminó: la categoría ya no existe
4. Los cambios se registran en auditoría
5. Si una categoría está inactiva, sus productos no aparecen en catálogo público

---

## CU-008: GESTIÓN DE MARCAS Y ESTILOS
**Actor(es):** Jefe
**Descripción:** Permite gestionar marcas comerciales y sus estilos asociados en el catálogo.

**Precondiciones:**
1. El jefe ha iniciado sesión
2. El módulo de marcas y estilos está disponible
3. La base de datos está operativa

**Flujo Principal - Crear Marca:**
1. El jefe navega a "Catálogo" → "Gestión de Marcas y Estilos"
2. El sistema presenta lista de marcas y opción crear
3. El jefe selecciona "Crear Nueva Marca"
4. El sistema presenta formulario:
   - Nombre de Marca (obligatorio, único)
   - Descripción (opcional)
   - Estado (activo/inactivo)
5. El jefe ingresa nombre único sin caracteres especiales
6. El jefe hace clic en "Crear"
7. El sistema valida unicidad
8. Si es válido:
   - Crea la marca
   - Muestra: "Marca creada exitosamente"

**Flujo Principal - Crear Estilo dentro de Marca:**
1. El jefe selecciona una marca
2. El jefe selecciona "Agregar Estilo"
3. El sistema presenta formulario:
   - Nombre de Estilo (obligatorio, único dentro de marca)
   - Descripción (opcional)
   - Estado (activo/inactivo)
4. El jefe ingresa nombre de estilo
5. El jefe hace clic en "Crear"
6. El sistema valida que el estilo sea único dentro de esa marca
7. Si es válido:
   - Crea el estilo
   - Lo vincula a la marca
   - Muestra: "Estilo creado exitosamente"

**Flujo Principal - Editar Marca o Estilo:**
1. El jefe selecciona la marca o estilo a editar
2. El jefe modifica nombre o descripción
3. El jefe hace clic en "Guardar"
4. El sistema valida unicidad
5. Si es válido, guarda cambios

**Flujo Principal - Eliminar Marca o Estilo:**
1. El jefe selecciona marca o estilo
2. El jefe hace clic en "Eliminar"
3. El sistema verifica si hay productos activos vinculados
4. Si NO hay vinculados:
   - Pide confirmación
   - Elimina
   - Muestra: "Eliminado exitosamente"
5. Si HAY vinculados:
   - Muestra: "No se puede eliminar. Hay X productos con esta marca/estilo"
   - Sugiere desactivar en su lugar

**Flujos Alternos:**
- **Nombre Duplicado:** Si el nombre existe, muestra: "El nombre ya existe"
- **Estilo No Único en Marca:** Si un estilo tiene mismo nombre en esa marca, muestra error
- **En Uso:** Si hay productos activos, impide eliminar

**Postcondiciones:**
1. Si se creó: marca o estilo está disponible para asignar productos
2. Si se editó: cambios reflejados en el sistema
3. Si se eliminó: ya no existe
4. Si está inactivo: productos no aparecen en catálogo público
5. Registrados en auditoría todos los cambios

---

## CU-009: VISUALIZACIÓN DE CATÁLOGO COMO VISITANTE
**Actor(es):** Visitante sin Registrar
**Descripción:** Permite a cualquier visitante sin autenticar consultar el catálogo público de productos.

**Precondiciones:**
1. La página principal está disponible
2. Existen productos registrados con estado "activo" y "público"
3. Los servicios de catálogo están operativos
4. El visitante tiene navegador con JavaScript habilitado

**Flujo Principal:**
1. El visitante accede a la página principal del sistema
2. El visitante selecciona "Ver Catálogo" o "Productos"
3. El sistema carga la página de catálogo público
4. El sistema muestra:
   - Imagen optimizada de cada producto
   - Nombre del producto
   - Referencia
   - Tallas disponibles
   - Colores disponibles
   - Material
   - Marca y estilo
   - NO muestra: precios, costos, cantidades en stock
5. El sistema permite aplicar filtros básicos:
   - Por Categoría (dropdown con categorías activas)
   - Por Marca
   - Por Estilo
   - Por Talla
   - Por Color
6. El visitante selecciona un filtro
7. El sistema aplica el filtro en menos de 3 segundos
8. El sistema actualiza resultados sin recargar página
9. El visitante puede ver detalles de un producto
10. El sistema muestra información completa del producto
11. Si intenta acceder a funciones restringidas (como realizar pedido):
    - El sistema muestra: "Debes crear una cuenta para acceder a esta funcionalidad"
    - Redirige a página de registro

**Flujos Alternos:**
- **Sin Productos:** Si no hay productos activos en el catálogo, muestra: "No hay productos disponibles en este momento"
- **Filtro sin Resultados:** Si los filtros aplicados no tienen coincidencias, muestra: "No se encontraron productos que coincidan con los criterios seleccionados"
- **Carga Lenta:** Si la carga excede 3 segundos, muestra: "Cargando..." con indicador de progreso

**Postcondiciones:**
1. El visitante ha consultado información de productos
2. Toda la navegación queda registrada para análisis de comportamiento
3. No se ha requerido autenticación
4. Solo se mostró información pública (sin precios ni inventario)

---

## CU-010: CONSULTA DE CATÁLOGO POR CLIENTE MAYORISTA
**Actor(es):** Cliente Mayorista Autenticado
**Descripción:** Permite al cliente mayorista autenticado consultar el catálogo completo para preparar pedidos.

**Precondiciones:**
1. El cliente mayorista ha iniciado sesión correctamente
2. Su cuenta está activa
3. El módulo de catálogo interno está disponible
4. Existen productos registrados con estado "activo"

**Flujo Principal:**
1. El cliente mayorista accede a su panel
2. Selecciona "Consultar Catálogo" o "Catálogo de Productos"
3. El sistema carga el catálogo interno filtrado
4. El sistema muestra todos los productos activos con:
   - Imagen principal optimizada
   - Nombre, referencia, tallas, colores, material, marca, estilo
   - Estado de disponibilidad en bodega (disponible/agotado/limitado)
   - Combinaciones con estado visual (verde=disponible, rojo=agotado)
5. El cliente puede guardar productos como favoritos:
   - Hace clic en ícono de estrella
   - El sistema registra el producto como favorito
   - Los favoritos se mantienen en la sesión y futuras sesiones
6. El cliente aplica filtros avanzados (mismos que visitante)
7. El cliente selecciona productos para crear pedido:
   - Selecciona un producto
   - Elige talla (solo disponibles)
   - Elige color (solo disponibles)
   - Ingresa cantidad
   - Hace clic en "Agregar al Pedido"
8. El sistema transfere automáticamente al formulario de pedido:
   - Crea pedido en estado "borrador"
   - Pre-llena con productos seleccionados
   - Permite continuar agregando o proceder al checkout

**Flujos Alternos:**
- **Combinación Agotada:** Si una talla-color está agotada, aparece deshabilitada con label "AGOTADO"
- **Sin Autenticación:** Si intenta acceder sin sesión, redirige a login
- **Sesión Expirada:** Si la sesión expiró, solicita nuevo login

**Postcondiciones:**
1. El cliente ha consultado información completa de productos
2. Sus favoritos están guardados
3. Si agregó productos, existe pedido en borrador
4. Todos los eventos quedan registrados

---

## CU-011: SISTEMA DE FILTRADO DE BÚSQUEDA
**Actor(es):** Usuario de Cualquier Rol
**Descripción:** Permite filtrar y buscar productos de forma eficiente en el catálogo.

**Precondiciones:**
1. El catálogo está disponible (público o interno)
2. Existen productos en el sistema
3. El motor de búsqueda está operativo

**Flujo Principal:**
1. El usuario accede al catálogo
2. El sistema muestra controles de filtrado:
   - Dropdown "Categoría" (múltiple selección)
   - Dropdown "Marca"
   - Dropdown "Estilo"
   - Dropdown "Talla"
   - Dropdown "Color"
   - Campo de búsqueda por texto libre
3. El usuario selecciona primer filtro (ej: Categoría = "Botas")
4. El sistema actualiza resultados en tiempo real
5. El usuario selecciona segundo filtro (ej: Marca = "Nike")
6. El sistema combina filtros (categoría AND marca)
7. El usuario selecciona tercer filtro (ej: Talla = "39")
8. El sistema aplica todos los filtros simultáneamente en menos de 2 segundos
9. El sistema actualiza nombre de producto e imagen sin recargar página
10. El usuario puede aplicar hasta 5 filtros simultáneamente
11. El usuario puede buscar por texto libre:
    - Ingresa palabra clave (ej: "oxford")
    - El sistema busca en nombre, referencia, descripción, marca y estilo
    - Busca coincidencias parciales
    - Devuelve resultados relevantes
12. El usuario puede limpiar filtros:
    - Hace clic en botón "Limpiar Filtros"
    - El sistema restaura vista general del catálogo
    - Todos los filtros se desmarcan

**Flujos Alternos:**
- **Sin Coincidencias:** Si combinación de filtros no tiene resultados, muestra: "No se encontraron productos que coincidan con los criterios seleccionados"
- **Búsqueda Sin Resultados:** Si texto libre no tiene resultados, muestra: "No se encontraron coincidencias. Intente con otros criterios"
- **Filtros Incompatibles:** Si la combinación de filtros es lógicamente imposible, muestra advertencia y desactiva opciones incompatibles

**Postcondiciones:**
1. Los resultados reflejan exactamente los filtros aplicados
2. El tiempo de respuesta es menor a 2 segundos incluso con 5,000+ productos
3. La paginación y resultados están organizados por relevancia
4. Todos los eventos de filtrado se registran para análisis

---

## CU-012: REALIZACIÓN DE PEDIDOS POR CLIENTE MAYORISTA
**Actor(es):** Cliente Mayorista Autenticado
**Descripción:** Permite al cliente mayorista registrar un pedido de productos.

**Precondiciones:**
1. El cliente mayorista ha iniciado sesión y está verificado
2. Su cuenta está activa
3. El módulo de pedidos está disponible
4. Existen productos en el catálogo
5. Se ha configurado la cantidad mínima de pedido

**Flujo Principal:**
1. El cliente mayorista navega a "Realizar Pedido" o "Crear Nuevo Pedido"
2. El cliente selecciona productos del catálogo
3. Para cada producto:
   - Selecciona talla (solo disponibles)
   - Selecciona color (solo disponibles)
   - Ingresa cantidad (números positivos)
   - El sistema valida que la cantidad sea mayor o igual a la mínima configurada
4. El cliente puede agregar múltiples productos
5. El sistema muestra un resumen con:
   - Lista de productos seleccionados con talla, color, cantidad
   - Cantidad total de pares
   - Verificación automática de disponibilidad en bodega
6. El cliente especifica la fecha de entrega requerida
7. El cliente hace clic en "Confirmar Pedido"
8. El sistema valida:
   - Cantidad total >= cantidad mínima configurable
   - Todos los campos obligatorios están completos
9. Si valida correctamente:
   - Para cada producto, verifica disponibilidad:
     * Si hay stock en bodega → marca como "aprobado para entrega"
     * Si no hay stock → marca como "pendiente de fabricación"
   - Genera número de pedido único (ej: PED-20260301-001)
   - Crea el pedido en estado "pendiente de revisión administrativa"
   - Registra: fecha, hora, cliente, detalle completo
   - Envía correo de confirmación al cliente
   - El sistema muestra: "Pedido registrado exitosamente. Número de pedido: PED-20260301-001"

**Flujos Alternos:**
- **Cantidad Menor a Mínimo:** Si cantidad < mínimo, muestra: "La cantidad mínima de pedido es X pares"
- **Sin Productos:** Si intenta confirmar sin productos, muestra: "Agregue al menos un producto"
- **Cantidad Inválida:** Si ingresa cantidad negativa o decimal, muestra: "Ingrese una cantidad válida"
- **Combinación No Existe:** Si selecciona talla-color no disponible, muestra: "Esta combinación no está disponible"

**Postcondiciones:**
1. El pedido existe en base de datos con estado "pendiente de revisión administrativa"
2. Se ha generado número único de pedido
3. El cliente ha recibido confirmación por correo
4. El jefe ha recibido notificación del nuevo pedido
5. El pedido muestra detalles: si es entrega directa o requiere fabricación

---

## CU-013: NOTIFICACIÓN DE NUEVOS PEDIDOS
**Actor(es):** Sistema, Jefe, Gerente Comercial, Planificador de Producción
**Descripción:** El sistema genera notificaciones automáticas cuando se registran nuevos pedidos.

**Precondiciones:**
1. Un cliente mayorista ha registrado un pedido exitosamente
2. El servicio de notificaciones está operativo
3. Los usuarios autorizados tienen cuentas activas

**Flujo Principal:**
1. El cliente mayorista confirma un nuevo pedido
2. El sistema inmediatamente (en menos de 5 segundos):
   - Genera notificación dirigida a:
     * Jefe del sistema
     * Gerente comercial
     * Planificador de producción
   - La notificación contiene:
     * ID del pedido
     * Nombre del cliente
     * Fecha y hora de registro
     * Cantidad total de ítems
     * Combinaciones solicitadas (talla, color, cantidad por producto)
     * Estado inicial del pedido
     * Indicación: "Entrega Directa" o "Pendiente de Fabricación"
3. Cada usuario recibe la notificación en su panel en tiempo real
4. Cada usuario recibe un correo de prioridad alta incluyendo:
   - Información resumida del pedido
   - Enlace directo a la ficha del pedido
   - Instrucciones de acción recomendada
5. Los usuarios pueden acceder al enlace directo y ver detalles completos del pedido
6. La notificación no es duplicada (una sola por pedido)
7. Todas las notificaciones se registran en historial del sistema

**Flujos Alternos:**
- **Error en Envío de Correo:** Si el correo falla, se registra el evento pero la notificación en panel se mantiene
- **Usuario Desconectado:** Si el usuario no está en línea, recibe la notificación cuando se conecte

**Postcondiciones:**
1. Los tres roles han sido notificados dentro de 5 segundos
2. Pueden acceder directamente al detalle del pedido
3. El evento está trazado en el historial
4. No hay duplicidad de notificaciones

---

## CU-014: CONSULTA DE ESTADO DE PEDIDOS POR CLIENTE
**Actor(es):** Cliente Mayorista Autenticado
**Descripción:** Permite al cliente mayorista consultar el estado y detalles de sus pedidos.

**Precondiciones:**
1. El cliente mayorista ha iniciado sesión
2. Tiene al menos un pedido registrado
3. El módulo de consulta de pedidos está disponible

**Flujo Principal:**
1. El cliente mayorista navega a "Mis Pedidos" o "Historial de Pedidos"
2. El sistema carga la lista de pedidos del cliente, ordenados cronológicamente (más recientes primero)
3. Para cada pedido muestra:
   - Número de pedido
   - Fecha de registro
   - Estado actual (pendiente, aprobado, en producción, completado, entregado, cancelado)
   - Cantidad total de pares
   - Visor de estado: barra de progreso o timeline
4. El cliente selecciona un pedido para ver detalles
5. El sistema abre panel de detalle que incluye:
   - Número y fecha del pedido
   - Estado actual con última actualización
   - Timeline de cambios de estado con fechas
   - Lista de productos con: referencia, talla, color, cantidad solicitada
   - Ruta de procesamiento (entrega directa o fabricación)
   - Porcentaje de avance de producción (si aplica)
   - Fecha prometida de entrega
6. El cliente puede aplicar filtros en la lista:
   - Por Estado (dropdown)
   - Por Rango de Fechas (date picker from-to)
   - Por Referencia de Producto
7. El sistema actualiza la lista automáticamente sin recargar
8. Si hay alertas de retraso:
   - El sistema detecta si fecha prometida < fecha actual y estado ≠ "completado"
   - Muestra banner rojo: "Este pedido está retrasado. Contacta con nosotros."

**Flujos Alternos:**
- **Sin Pedidos:** Si el cliente no tiene pedidos, muestra: "No tienes pedidos registrados. Crea uno nuevo"
- **Acceso No Autorizado:** Si intenta acceder a pedido de otro cliente por URL, muestra: "Acceso no autorizado" y registra en bitácora de seguridad
- **Filtro sin Resultados:** Si aplica filtros sin coincidencias, muestra: "No se encontraron pedidos que coincidan con los criterios"

**Postcondiciones:**
1. El cliente ha visualizado información completa de sus pedidos
2. Tiene actualización del progreso de producción (latencia máxima 5 minutos)
3. Todos los eventos quedan registrados en historial

---

## CU-015: ACTUALIZACIÓN DE ESTADO DE PEDIDOS POR JEFE
**Actor(es):** Jefe, Gerente Comercial
**Descripción:** Permite al jefe cambiar el estado de los pedidos en el flujo de procesamiento.

**Precondiciones:**
1. El jefe ha iniciado sesión
2. Existen pedidos en el sistema
3. El módulo de gestión de pedidos está disponible
4. Los cambios de estado son lógicamente válidos

**Flujo Principal:**
1. El jefe navega a "Gestión de Pedidos" → "Ver Pedidos"
2. El sistema muestra lista de pedidos con estados actuales
3. El jefe selecciona un pedido para cambiar estado
4. El sistema abre panel de detalles
5. El jefe selecciona el nuevo estado deseado de una lista de opciones válidas:
   - De "pendiente" puede cambiar a: "aprobado", "cancelado"
   - De "aprobado" puede cambiar a: "en producción", "aprobado para entrega", "cancelado"
   - De "en producción" puede cambiar a: "fabricado", "completado"
   - De "fabricado" puede cambiar a: "aprobado para entrega"
   - De "aprobado para entrega" puede cambiar a: "entregado", "cancelado"
6. El jefe es obligatorio ingresar un "Motivo del Cambio" (campo de texto)
7. El jefe hace clic en "Confirmar Cambio"
8. El sistema ejecuta acciones según el cambio:
   - Si cambia a "aprobado":
     * Genera automáticamente orden de producción asociada con mismo ID
     * Marca los insumos como "reservados" en inventario
   - Si cambia a "aprobado para entrega":
     * Verifica disponibilidad en bodega
     * Si hay stock, acepta el cambio
     * Si no hay stock, muestra: "No hay disponibilidad en bodega"
   - Si cambia a "entregado":
     * Valida que haya pasado por "aprobado para entrega"
     * Si no pasó, muestra: "El pedido no puede ser marcado como entregado sin pasar por 'aprobado para entrega'"
   - Si cambia a "cancelado":
     * Registra la causa de cancelación
     * Libera los insumos reservados si aplica
9. El sistema registra el cambio:
   - Estado anterior
   - Nuevo estado
   - Fecha y hora
   - Usuario responsable (ID del jefe)
   - Motivo del cambio
10. El sistema muestra: "Estado actualizado correctamente"
11. El cliente mayorista recibe notificación del cambio de estado

**Flujos Alternos:**
- **Cambio No Permitido:** Si intenta transición inválida, muestra: "No se puede cambiar de [estado actual] a [estado solicitado]"
- **Sin Disponibilidad:** Si cambia a "aprobado para entrega" sin stock, muestra: "No hay disponibilidad de este producto en bodega"
- **Sin Motivación:** Si intenta cambiar sin ingresar motivo, muestra: "El motivo es obligatorio"

**Postcondiciones:**
1. El estado del pedido ha sido actualizado
2. Se han ejecutado las acciones automáticas (orden de producción, reserva de insumos, etc.)
3. El cliente ha sido notificado
4. El historial del pedido muestra el cambio con todos los detalles
5. El evento está registrado en auditoría

---

## CU-016: GESTIÓN DE INVENTARIO DE CALZADO FABRICADO
**Actor(es):** Jefe, Jefe de Bodega
**Descripción:** Permite gestionar el inventario de productos terminados en bodega.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o jefe de bodega
2. El módulo de gestión de inventario está disponible
3. Existen productos en el catálogo

**Flujo Principal - Registrar Ingreso:**
1. El jefe navega a "Inventario" → "Registrar Movimiento"
2. El sistema presenta formulario con el tipo "Entrada de Mercancía"
3. El jefe especifica:
   - Referencia del producto (dropdown del catálogo)
   - Talla (dropdown)
   - Color (dropdown)
   - Cantidad (número positivo)
   - Fecha de ingreso
   - Origen ("producción interna", "devolución de cliente", etc.)
4. El jefe hace clic en "Registrar"
5. El sistema valida:
   - Que la referencia exista en catálogo
   - Que la cantidad sea positiva
6. Si valida:
   - Si la combinación ya existe en inventario: incrementa cantidad
   - Si la combinación es nueva: crea nuevo registro con estado "disponible"
   - Actualiza el conteo total de esa SKU
   - Registra: fecha, hora, usuario, origen
   - Muestra: "Movimiento registrado exitosamente"

**Flujo Principal - Consultar Inventario:**
1. El jefe navega a "Inventario" → "Consultar Stock"
2. El sistema muestra tabla con todas las SKUs:
   - Referencia, marca, estilo, talla, color
   - Cantidad disponible
   - Cantidad reservada (en pedidos aprobados)
   - Cantidad en restauración
   - Stock total
3. El jefe puede aplicar filtros:
   - Por Referencia
   - Por Marca
   - Por Talla
   - Por Color
4. El jefe puede hacer clic en una SKU para ver detalles:
   - Historial de movimientos de entrada y salida
   - Responsables de cada movimiento
   - Fechas de cada operación

**Flujo Principal - Realizar Ajuste Manual:**
1. El jefe selecciona una SKU
2. El jefe hace clic en "Hacer Ajuste"
3. El sistema presenta formulario:
   - Cantidad actual (pre-lleno, no editable)
   - Nueva cantidad (campo editable)
   - Motivo del ajuste (field obligatorio: conteo físico, corrección, etc.)
4. El jefe ingresa la nueva cantidad
5. El jefe selecciona el motivo
6. El jefe hace clic en "Confirmar Ajuste"
7. El sistema calcula la diferencia:
   - Si nueva > actual: registra como "entrada por ajuste"
   - Si nueva < actual: registra como "salida por ajuste"
8. El sistema actualiza el inventario
9. Registra el ajuste en el historial con motivo

**Flujos Alternos:**
- **Cantidad Negativa:** Si intenta ingresar cantidad negativa, muestra: "Ingrese una cantidad válida (mayor a cero)"
- **SKU No Existe:** Si la referencia no está en catálogo, muestra: "Referencia no existe. Cree el producto primero"
- **Stock Insuficiente:** Si intenta reducir más allá de disponible, muestra: "Stock insuficiente para esta operación"

**Postcondiciones:**
1. El inventario refleja movimientos de entrada y salida
2. No hay SKUs con cantidad negativa
3. Todos los movimientos quedan trazados en historial
4. Los cambios se registran en auditoría

---

## CU-017: ACTUALIZACIÓN AUTOMÁTICA DEL INVENTARIO
**Actor(es):** Sistema
**Descripción:** El sistema actualiza automáticamente el inventario cuando termina la producción.

**Precondiciones:**
1. Una orden de producción ha sido completada
2. Ha sido aprobada por control de calidad
3. El sistema de inventario está operativo

**Flujo Principal:**
1. Un operario marca una orden de producción como "fabricada"
2. El jefe de calidad revisa y aprueba la orden (estado "finalizada")
3. El sistema automáticamente:
   - Valida que la orden esté en estado "en fabricación"
   - Verifica que todas las combinaciones requeridas hayan sido completadas
   - Para cada combinación de talla-color:
     * Si ya existe en inventario: incrementa cantidad disponible en esa cantidad
     * Si no existe: crea nuevo registro en inventario con estado "disponible"
   - Registra automáticamente:
     * Fecha de ingreso
     * Número de orden de producción que originó el ingreso
     * Origen: "producción interna"
4. El sistema valida la cantidad ingresada vs. cantidad esperada:
   - Si diferencia <= 2% de tolerancia: acepta el ingreso
   - Si diferencia > 2%:
     * Genera incidencia de prioridad alta para el jefe
     * Registra la discrepancia
     * Lo etiqueta con el motivo: "diferencia en cantidad producida"
5. El sistema impide duplicidad:
   - Verifica que esta orden no fue procesada anteriormente
   - Si ya fue procesada, rechaza la acción para evitar doble conteo
6. El sistema muestra confirmación: "Inventario actualizado automáticamente"

**Flujos Alternos:**
- **Diferencia > 2%:** Si la cantidad difiere más del 2%, genera alerta al jefe con los detalles
- **Ya Procesada:** Si la orden ya fue ingresada a inventario anteriormente, rechaza: "Esta orden ya fue procesada"
- **Combinaciones Incompletas:** Si no todas las combinaciones fueron completadas, rechaza: "Todas las combinaciones requeridas deben estar completadas"

**Postcondiciones:**
1. El inventario ha sido actualizado automáticamente
2. El ingreso está vinculado a la orden de producción
3. Si hay discrepancias, están documentadas y alertadas
4. Se registra automáticamente en el historial de inventario

---

## CU-018: REGISTRO DE VENTAS Y DESCUENTO EN INVENTARIO
**Actor(es):** Jefe, Personal Comercial
**Descripción:** Permite registrar ventas manuales que no provienen de pedidos en el sistema.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o personal comercial
2. El módulo de ventas manuales está disponible
3. Existen productos en inventario disponibles

**Flujo Principal:**
1. El usuario navega a "Inventario" → "Registrar Venta Manual"
2. El sistema presenta formulario con campos:
   - Referencia del producto (dropdown)
   - Talla (dropdown)
   - Color (dropdown)
   - Cantidad vendida (número)
   - Fecha de salida
   - Destino de la venta (cliente, descripción)
   - Motivo de la venta (venta directa, promoción, etc.)
   - Responsable (nombre usuario, pre-lleno)
3. El usuario completa el formulario
4. El usuario hace clic en "Validar Stock"
5. El sistema verifica disponibilidad en tiempo real:
   - Consulta cantidad disponible de esa SKU
   - Si stock >= cantidad solicitada:
     * Muestra: "Stock disponible: X pares"
     * Habilita botón "Confirmar Venta"
   - Si stock < cantidad solicitada:
     * Muestra: "El stock es insuficiente para esta salida. Stock disponible: X pares"
     * Desactiva botón confirmar
6. Si stock es suficiente, el usuario hace clic en "Confirmar Venta"
7. El sistema:
   - Descuenta la cantidad del inventario
   - Crea registro de movimiento "salida por venta directa"
   - Registra: fecha, hora, motivo, responsable, cliente
   - Muestra: "Venta registrada exitosamente"
8. El saldo resultante se actualiza en tiempo real

**Flujos Alternos:**
- **Stock Insuficiente:** Si hay menos stock del solicitado, rechaza la venta
- **Cantidad Inválida:** Si ingresa cantidad negativa o cero, muestra: "Ingrese una cantidad válida"
- **Duplicación:** Si intenta registrar nuevamente la misma venta en corto plazo, detecta y bloquea

**Postcondiciones:**
1. El inventario ha sido reducido en la cantidad vendida
2. El movimiento está registrado como "salida por venta directa"
3. El cliente o destino está documentado
4. Todos los detalles quedan en historial de inventario

---

## CU-019: REGISTRO DE PÉRDIDAS POR CALZADO DEFECTUOSO
**Actor(es):** Jefe, Personal de Control de Calidad
**Descripción:** Permite registrar pérdidas de inventario por productos defectuosos.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o control de calidad
2. El módulo de registro de pérdidas está disponible
3. Se ha predefinido una lista de códigos de defecto

**Flujo Principal:**
1. El usuario navega a "Inventario" → "Registrar Pérdida por Defecto"
2. El sistema presenta formulario con campos:
   - Referencia del producto (dropdown)
   - Talla (dropdown)
   - Color (dropdown)
   - Cantidad descartada (número)
   - Fecha del evento
   - Código de Defecto (dropdown: rotura, costura defectuosa, material defectuoso, etc.)
   - Observaciones (opcional)
3. El usuario completa el formulario
4. El usuario hace clic en "Registrar Pérdida"
5. El sistema valida:
   - Que la combinación exista en inventario
   - Que la cantidad no exceda stock disponible
6. Si el usuario es operario:
   - La pérdida se registra con estado "pendiente de aprobación de calidad"
   - Se envía notificación al jefe de calidad
   - El inventario NO se modifica aún
7. Si el usuario es jefe de calidad:
   - La pérdida se aprueba inmediatamente
   - El inventario de "productos terminados" se reduce
   - El inventario de "scrap stock" (stock dañado) se incrementa
   - Se registra la pérdida con motivo del defecto
8. El sistema registra: fecha, hora, código de defecto, cantidad, usuario responsable
9. El sistema muestra confirmación

**Flujos Alternos:**
- **Cantidad Excede Stock:** Si cantidad > stock disponible, muestra: "La cantidad excede el stock disponible"
- **Sin Código Defecto:** Si no selecciona código de defecto, muestra: "Seleccione un código de defecto"
- **Operario sin Aprobación:** Si es operario, la pérdida no se aplica al inventario hasta aprobación del jefe

**Postcondiciones:**
1. Si fue aprobada: el inventario de "productos terminados" se redujo y "scrap stock" se incremen tó
2. Si está pendiente: queda registrada con estado "pendiente de aprobación"
3. Todos los detalles están en historial

---

## CU-020: PROCESO DE RESTAURACIÓN DE CALZADO DEFECTUOSO
**Actor(es):** Jefe, Jefe de Calidad
**Descripción:** Permite gestionar el proceso de restauración de productos defectuosos descartados.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o jefe de calidad
2. Existen productos en "scrap stock" o historial de pérdidas
3. El módulo de restauración está disponible

**Flujo Principal:**
1. El usuario navega a "Inventario" → "Proceso de Restauración"
2. El sistema muestra lista de productos en "scrap stock"
3. El usuario selecciona un producto para restaurar
4. El sistema presenta formulario para iniciar restauración:
   - Referencia (pre-lleno)
   - Talla (pre-lleno)
   - Color (pre-lleno)
   - Cantidad a restaurar (número, no puede exceder cantidad descartada)
   - Tipo de intervención (acabado, reparación de costura, cambio de material, etc.)
   - Fecha de inicio
5. El usuario completa el formulario
6. El usuario hace clic en "Iniciar Restauración"
7. El sistema:
   - Valida que cantidad a restaurar <= cantidad descartada
   - Marca las unidades como "en restauración"
   - Bloquea su visualización en inventario disponible
   - Registra fecha de inicio
8. Durante el proceso:
   - Las unidades están en estado "en restauración" (no disponibles para venta)
   - El jefe de calidad revisa el trabajo periódicamente
9. Cuando se completa la restauración:
   - El usuario registra fecha de finalización
   - El usuario selecciona "Resultado Final":
     * "Restaurado y Aprobado" → para reincorporar al inventario
     * "No Recuperable" → para mantener como pérdida definitiva
10. Si selecciona "Aprobado":
    - El sistema mueve las unidades a "productos terminados"
    - Registra el costo de reparación en el historial del producto
    - Actualiza el costo unitario si aplica
    - El usuario hace clic en "Confirmar Aprobación"
    - El sistema muestra: "Productos restaurados y reincorporados al inventario"
11. Si selecciona "No Recoverable":
    - Las unidades permanecen en "scrap stock"
    - Se registra como pérdida definitiva
    - El usuario hace clic en "Confirmar Rechazo"
    - El sistema muestra: "Restauración rechazada. Los productos permanecen como pérdida"

**Flujos Alternos:**
- **Cantidad Excede Descarte:** Si intenta restaurar más de lo disponible, muestra: "La cantidad excede el total descartado"
- **Sin Aprobación de Calidad:** Si no es jefe de calidad, puede iniciar pero no puede finalizar

**Postcondiciones:**
1. Si se aprobó: los productos están en inventario disponible con costo actualizado
2. Si se rechazó: los productos permanecen en "scrap stock" como pérdida
3. Todo el proceso está trazado en historial de restauración

---

## CU-021: CREACIÓN DE TAREAS POR EL JEFE
**Actor(es):** Jefe, Planificador de Producción
**Descripción:** Permite crear tareas operativas vinculadas a órdenes de producción.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o planificador
2. El módulo de gestión de tareas está disponible
3. Existen órdenes de producción en el sistema

**Flujo Principal:**
1. El usuario navega a "Tareas" → "Crear Nueva Tarea"
2. El sistema presenta formulario con campos:
   - Título (obligatorio, descripción breve)
   - Descripción detallada (obligatorio)
   - Tipo de Tarea (dropdown: proceso, embalaje, control, etc.)
   - Prioridad (alta, normal, baja)
   - Fecha Límite Estimada (obligatorio)
   - Referencia a Orden de Producción (obligatorio, dropdown)
3. El usuario completa todos los campos
4. El usuario hace clic en "Validar y Crear"
5. El sistema valida:
   - Que todos los campos obligatorios estén completos
   - Que la orden de producción exista
6. El sistema consulta la ficha del modelo en la orden de producción:
   - Obtiene el tiempo estándar definido para el proceso
   - Este tiempo es no editable y se asigna automáticamente a la tarea
7. El sistema verifica si no existe tarea duplicada:
   - Busca si hay otra tarea para la misma orden y mismo tipo de proceso
   - Si existe, muestra: "Ya existe una tarea de este tipo para esta orden"
8. Si todo es válido:
   - Crea la tarea en base de datos con estado "pendiente de inicio"
   - Sistema asigna identificador único (TK-20260301-001)
   - Registra: fecha, hora, usuario creador
   - Muestra: "Tarea creada exitosamente"
9. Tras creación, la tarea está lista para asignar a empleado

**Flujos Alternos:**
- **Campos Incompletos:** Si faltan campos obligatorios, muestra: "Complete todos los campos obligatorios"
- **Orden No Existe:** Si la orden de producción no existe, muestra: "Orden de producción no válida"
- **Tarea Duplicada:** Si ya existe tarea similar para la misma orden, muestra: "Ya existe una tarea de este tipo"
- **Fecha Límite Anterior:** Si la fecha límite es anterior a hoy, muestra: "La fecha límite debe ser futura"

**Postcondiciones:**
1. La tarea existe en base de datos con estado "pendiente de inicio"
2. El tiempo estándar está asignado (automático, no editable)
3. Está lista para ser asignada a un empleado
4. Se ha registrado en auditoría

---

## CU-022: ASIGNACIÓN DE TAREAS A EMPLEADOS
**Actor(es):** Jefe, Planificador de Producción
**Descripción:** Permite asignar tareas creadas a empleados específicos.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o planificador
2. Existen tareas en estado "pendiente de inicio"
3. Existen empleados activos en el sistema
4. El módulo de asignación está disponible

**Flujo Principal:**
1. El usuario navega a "Tareas" → "Asignar Tareas"
2. El sistema muestra lista de tareas pendientes disponibles para asignar
3. El usuario selecciona una tarea
4. El sistema abre panel de asignación con:
   - Detalles de la tarea (título, descripción, tipo, prioridad, tiempo estándar)
   - Dropdown de empleados activos del sistema
   - Campo para fecha límite de ejecución (obligatorio)
   - Campo de observaciones opcionales
5. El usuario selecciona un empleado
6. El usuario especifica la fecha límite en que el empleado debe completar la tarea
7. El user hace clic en "Validar Carga de Trabajo"
8. El sistema calcula:
   - Tareas actuales asignadas al empleado para ese día
   - Tiempo total estimado vs. horas laborales disponibles
   - % de ocupación = (tiempo total / horas disponibles) * 100
9. Si ocupación <= 100%:
   - El sistema permite continuar sin advertencia
10. Si ocupación > 100%:
    - El sistema muestra advertencia: "El empleado tendrá X% de ocupación. ¿Deseas continuar?"
    - El usuario puede continuar o cancelar la asignación
11. El usuario hace clic en "Confirmar Asignación"
12. El sistema:
    - Asigna la tarea al empleado
    - Cambia estado de la tarea a "asignada"
    - Bloquea la tarea para edición por otros usuarios (solo el jefe puede editar)
    - Registra: fecha, hora, empleado asignado
    - Envía notificación in-app al empleado: "Se te ha asignado una nueva tarea"
    - Envía correo al empleado con detalles: título, descripción, fecha límite, observaciones
    - Muestra: "Tarea asignada exitosamente"
13. La tarea aparece en el panel del empleado con estado "pendiente de inicio"

**Flujos Alternos:**
- **Empleado Inactivo:** Si selecciona empleado con cuenta no activa, muestra: "Seleccione un empleado con cuenta activa"
- **Sin Empleados:** Si no hay empleados disponibles, muestra: "No hay empleados activos para asignar"
- **Ocupación Excesiva:** Si excede 110%, se muestra advertencia pero permite asignar

**Postcondiciones:**
1. La tarea está asignada a un empleado específico
2. El estado es "asignada"
3. El empleado ha recibido notificación in-app y por correo
4. Aparece en el panel personal del empleado
5. La asignación está registrada en auditoría

---

## CU-023: CONSULTA DE TAREAS ASIGNADAS POR EL EMPLEADO
**Actor(es):** Empleado Autenticado
**Descripción:** Permite al empleado consultar sus tareas asignadas y organizarlas.

**Precondiciones:**
1. El empleado ha iniciado sesión
2. Tiene tareas asignadas en el sistema
3. El módulo de consulta de tareas está disponible

**Flujo Principal:**
1. El empleado accede a su panel
2. Selecciona "Mis Tareas" o "Tareas Asignadas"
3. El sistema carga el panel de tareas del empleado
4. El sistema muestra todas las tareas asignadas organizadas cronológicamente
5. Para cada tarea muestra:
   - Título de la tarea
   - Descripción resumida
   - Tipo de tarea
   - Prioridad (alta/normal/baja)
   - Fecha de asignación
   - Fecha límite
   - Estado (pendiente, en progreso, completada, cancelada)
   - Observaciones del jefe
   - Indicador de tiempo restante: "X días para la fecha límite"
6. Las tareas están clasificadas por estado:
   - **Activas (Pendiente/En Progreso):** mostradas en primer plano, ordenadas por fecha límite
   - **Completadas:** mostradas en segundo plano, con opción de ocultar
7. El empleado puede filtrar por:
   - Estado (dropdown)
   - Prioridad (alta, normal, baja)
   - Rango de fechas límite
8. El empleado puede hacer clic en una tarea para ver detalles completos:
   - Descripción completa
   - Orden de producción vinculada
   - Todos los detalles de la asignación
9. El empleado puede ver tareas completadas como historial:
   - Están disponibles para consulta pero no se pueden editar
   - Muestran el estado "completada"

**Flujos Alternos:**
- **Sin Tareas:** Si el empleado no tiene tareas asignadas, muestra: "No tienes tareas asignadas en este momento"
- **Filtro sin Resultados:** Si aplica filtros sin coincidencias, muestra: "No se encontraron tareas que coincidan con los criterios"

**Postcondiciones:**
1. El empleado ha visualizado todas sus tareas
2. Sabe exactamente qué hacer y hasta cuándo
3. Puede organizar su carga de trabajo
4. Todos los eventos quedan registrados

---

## CU-024: REPORTE DE AVANCES E INCIDENCIAS EN TAREAS
**Actor(es):** Empleado Asignado a una Tarea
**Descripción:** Permite al empleado registrar avances parciales e incidencias durante la ejecución.

**Precondiciones:**
1. El empleado tiene una tarea en estado "asignada" o "en progreso"
2. El módulo de reporte de avances está disponible
3. El servicio de timestamps del servidor está operativo

**Flujo Principal - Iniciar Tarea:**
1. El empleado abre el panel de una tarea en estado "pendiente de inicio"
2. El empleado hace clic en "Iniciar Tarea"
3. El sistema registra marca de tiempo inicial del servidor
4. El Estado cambia a "en progreso"
5. El sistema presenta panel de ejecución con:
   - Cronómetro en vivo
   - Botones: "Pausar", "Reanudar", "Finalizar"
   - Campo de "Porcentaje Completado" (actualizable)
   - Sección para reportar observaciones

**Flujo Principal - Registro de Avances:**
1. Durante la ejecución, el empleado puede:
   - Actualizar "Porcentaje de Avance" (campos: 0, 25, 50, 75, 100%)
   - Registrar observaciones técnicas
   - El sistema calcula tiempo real invertido
2. El empleado puede pausar la tarea:
   - Hace clic en "Pausar"
   - Especifica motivo de pausa (descanso, espera de materiales, etc.)
   - El cronómetro detiene
   - El empleado puede reanudar después
3. Si la pausa supera el 10% del tiempo estándar:
   - El sistema solicita justificación: "¿Motivo de la pausa prolongada?"
   - El empleado debe ingresar justificación en campo de texto

**Flujo Principal - Reportar Incidencia:**
1. Si ocurre un problema durante la ejecución:
   - El empleado hace clic en "Reportar Incidencia"
   - El sistema abre formulario con campos:
     * Tipo de Incidencia (dropdown: rotura de máquina, falta de insumos, etc.)
     * Descripción detallada
     * Impacto sobre la tarea (total/parcial)
2. El empleado completa el formulario
3. El empleado hace clic en "Registrar Incidencia"
4. El sistema:
   - Registra la incidencia con timestamp
   - Si el impacto es "total":
     * Cambia automáticamente el estado de la tarea a "bloqueada"
     * Envía notificación al jefe: "Tarea bloqueada por incidencia crítica"
   - Si es tipo "rotura de máquina":
     * Genera ticket de mantenimiento
     * Notifica al jefe de mantenimiento en menos de 60 segundos
   - Si es tipo "falta de insumo crítico":
     * Genera ticket urgente al jefe de compras
5. El sistema muestra: "Incidencia registrada y reportada"

**Flujos Alternos:**
- **Dos Tareas Simultáneas:** Si intenta iniciar dos tareas al mismo tiempo, muestra: "Solo puedes tener una tarea en progreso"
- **Pausa Prolongada:** Si pausa supera 10% del tiempo estándar, solicita justificación obligatoria
- **100% sin Confirmación:** Si marca 100% de avance, requiere confirmación adicional

**Postcondiciones:**
1. Todos los avances quedan registrados en la tarea
2. Se ha calculado tiempo real invertido y eficiencia
3. Si hay incidencias, están documentadas y han generado tickets
4. Los eventos están trazados en el historial

---

## CU-025: CONFIRMACIÓN DE FINALIZACIÓN DE TAREAS
**Actor(es):** Empleado Asignado a una Tarea
**Descripción:** Permite al empleado confirmar formalmente la finalización de una tarea.

**Precondiciones:**
1. El empleado ha completado una tarea
2. La tarea está en estado "asignada" o "en progreso"
3. El módulo de confirmación está disponible

**Flujo Principal:**
1. El empleado cuando termina su trabajo hace clic en "Confirmar Finalización"
2. El sistema presenta formulario de confirmación con campos:
   - Resumen del Trabajo Realizado (texto obligatorio)
   - Cantidad Exacta de Pares Procesados (número obligatorio)
   - Evidencia Fotográfica (adjunto opcional, para procesos críticos)
   - Informe Final (adjunto opcional)
   - Observaciones Técnicas (texto opcional)
   - Fecha de Finalización (pre-lleno con fecha actual)
3. El empleado completa el formulario
4. El empleado hace clic en "Validar Finalización"
5. El sistema valida:
   - Que la cantidad reportada esté dentro del ±1% de la cantidad asignada
   - Si está dentro: continúa
   - Si está fuera del 1%:
     * Muestra: "Existe discrepancia entre cantidad asignada y reportada. Se requiere clave de supervisor."
     * Solicita campo de "Clave Supervisor"
     * El empleado ingresa clave de supervisor
     * El sistema valida la clave
     * Si es válida: registra la diferencia como "desperdicio" o "falta", continúa
     * Si es inválida: rechaza
6. Una vez validada la cantidad:
   - El sistema cambia estado de la tarea a "completada"
   - El sistema bloquea la tarea para cualquier edición posterior
   - El sistema registra: fecha/hora finalización, cantidad procesada, empleado
   - El sistema calcula eficiencia = tiempo estándar / tiempo real
   - El sistema envía notificación al jefe: "La tarea [nombre] ha sido finalizada"
   - El sistema muestra: "Tarea finalizada exitosamente"
7. Si esta era la última tarea de la orden de producción:
   - El sistema genera automáticamente alerta a control de calidad
   - Cambia estado de la orden a "pendiente de inspección de calidad"

**Flujos Alternos:**
- **Cantidad Fuera de Rango:** Si la discrepancia > 1%, requiere clave de supervisor
- **Sin Cantidad:** Si no ingresa cantidad, muestra: "Es obligatorio ingresar la cantidad"
- **Tarea Anterior no Completada:** Si hay tareas precedentes incompletas, muestra advertencia

**Postcondiciones:**
1. La tarea tiene estado "completada" y está bloqueada para edición
2. Se ha calculado eficiencia
3. El jefe ha sido notificado automáticamente
4. Todos los detalles están en el historial
5. Si es la última de una orden, se genera alerta de calidad

---

## CU-026: NOTIFICACIÓN AL JEFE DE TAREAS FINALIZADAS
**Actor(es):** Sistema, Jefe, Supervisor de Línea
**Descripción:** El sistema genera automáticamente una notificación dirigida al jefe cada vez que un empleado marca una tarea como finalizada, incluyendo resumen de desempeño con eficiencia calculada.

**Precondiciones:**
1. Un empleado ha marcado una tarea como "completada"
2. El módulo de notificaciones está operativo
3. El jefe tiene cuenta activa

**Flujo Principal:**
1. El empleado confirma finalización de una tarea
2. El sistema valida que todos los datos estén completos
3. Si es válido:
   - El sistema calcula eficiencia = tiempo estándar / tiempo real
   - El sistema genera notificación automática dirigida a jefe y supervisor
   - La notificación incluye:
     * Número y título de la tarea
     * Nombre del empleado responsable
     * Fecha de finalización
     * Eficiencia calculada (porcentaje)
     * Resumen del trabajo realizado
     * Cantidad de pares procesados
     * Enlace directo a la evidencia si existe
4. El sistema envía notificación in-app en menos de 30 segundos
5. Si está habilitado, envía también por correo electrónico
6. El jefe recibe la notificación en su panel
7. El jefe puede hacer clic en la notificación y acceder directamente a:
   - Detalles completos de la tarea
   - Evidencia fotográfica (si se adjuntó)
   - Observaciones técnicas
8. El jefe puede hacer clic en botones:
   - "Aprobar": acepta la tarea como completada
   - "Rechazar": rechaza la tarea y solicita retrabajo
9. Si rechaza:
   - El jefe debe ingresar motivo específico (campo obligatorio)
   - El jefe hace clic en "Confirmar Rechazo"
   - El sistema:
     * Cambia estado de la tarea a "reabierta"
     * Registra el motivo del rechazo
     * El tiempo anterior se anula y comienza en cero
     * Envía notificación al empleado: "Tu tarea [nombre] ha sido reabierta. Motivo: [motivo]"
     * Registra el retrabajo en el historial

**Flujos Alternos:**
- **Sin Datos Completos:** Si el empleado no completó todos los campos, el sistema retiene la notificación hasta completarse
- **Error en Envío:** Si el correo falla, registra el evento pero la notificación in-app se mantiene visible

**Postcondiciones:**
1. El jefe ha sido notificado dentro de 30 segundos
2. Puede acceder directamente a todos los detalles de la tarea
3. Ha aprobado o rechazado la tarea
4. Si rechaza, el empleado es notificado y comienza retrabajo
5. Todos los eventos están registrados en auditoría

---

## CU-027: MODIFICACIÓN Y ELIMINACIÓN DE TAREAS POR EL JEFE
**Actor(es):** Jefe, Planificador de Producción
**Descripción:** Permite al jefe editar o eliminar tareas siempre que no hayan sido completadas o canceladas.

**Precondiciones:**
1. El jefe ha iniciado sesión
2. Existen tareas en estado editable
3. El módulo de gestión de tareas está disponible

**Flujo Principal - Editar Tarea:**
1. El jefe navega a "Tareas" → "Gestionar Tareas"
2. El jefe selecciona una tarea en estado "pendiente" o "asignada"
3. El jefe hace clic en "Editar"
4. El sistema abre panel con campos editables:
   - Título, descripción, tipo, prioridad, fecha límite
   - Empleado asignado (si está asignada)
   - Observaciones
5. El jefe modifica los campos necesarios
6. El jefe hace clic en "Guardar Cambios"
7. El sistema valida que los datos sean válidos
8. Si es válido:
   - Actualiza la tarea
   - Si cambió empleado asignado, notifica al nuevo empleado
   - Registra en historial: campo modificado, valor anterior, valor nuevo, usuario responsable
   - Muestra: "Cambios guardados exitosamente"

**Flujo Principal - Eliminar Tarea:**
1. El jefe selecciona una tarea
2. El jefe hace clic en "Eliminar"
3. El sistema valida:
   - Si está en estado "pendiente" y sin reportes de avance: permite eliminar
   - Si tiene registro de tiempo > 0: bloquea eliminación
4. Si puede eliminarse:
   - Pide confirmación explícita
   - El jefe confirma
   - El sistema elimina la tarea
   - Registra: "Tarea eliminada por [usuario] en [fecha]"
5. Si no puede eliminarse:
   - Muestra: "No se puede eliminar. Esta tarea tiene registro de tiempo."
   - Sugiere opción de "cancelar" en su lugar
6. Si el jefe opta por "cancelar":
   - El formulario solicita motivo de cancelación (obligatorio)
   - El jefe ingresa motivo
   - El jefe hace clic en "Confirmar Cancelación"
   - El estado cambia a "cancelada"
   - Registra el motivo en el historial

**Flujos Alternos:**
- **Con Registro de Tiempo:** Si la tarea tiene tiempo, impide eliminación y obliga a cancelar
- **Asignada a Empleado:** Si está asignada, notifica al empleado del cambio
- **Sin Confirmación:** Si intenta eliminar sin confirmar, solicita confirmación nuevamente

**Postcondiciones:**
1. Si se editó: los cambios están reflejados y registrados
2. Si se eliminó: la tarea ya no existe
3. Si se canceló: está en estado "cancelada" con motivo documentado
4. El empleado fue notificado de cambios si aplica
5. Todos los eventos están en auditoría

---

## CU-028: REGISTRO DE INCIDENCIAS DE MAQUINARIA E INSUMOS
**Actor(es):** Empleado Asignado a una Tarea
**Descripción:** Permite al empleado reportar incidencias de maquinaria o falta de insumos durante la ejecución de tareas.

**Precondiciones:**
1. El empleado tiene una tarea en progreso
2. Ha ocurrido una incidencia que impide continuar
3. El módulo de registro de incidencias está disponible

**Flujo Principal:**
1. El empleado durante la tarea hace clic en "Reportar Incidencia"
2. El sistema presenta formulario con campos:
   - Tipo de Incidencia (dropdown):
     * Rotura de máquina
     * Falta de insumo crítico
     * Falta de insumo no crítico
     * Condición insegura
     * Otro
   - Descripción detallada del problema (obligatorio)
   - Código/nombre del equipo o insumo afectado (obligatorio)
   - Foto de la prueba (adjunto obligatorio para máquina)
   - Área afectada (obligatorio)
   - Impacto sobre la tarea:
     * Total (task se bloquea)
     * Parcial (task continúa con advertencia)
   - Fecha y hora (pre-lleno con servidor)
3. El empleado completa todos los campos obligatorios
4. El empleado hace clic en "Registrar Incidencia"
5. El sistema valida que los datos estén completos
6. El sistema registra la incidencia con ID único (INC-20260301-001)
7. Según el tipo y el impacto:
   - Si tipo = "rotura de máquina":
     * Genera ticket de mantenimiento con prioridad "alta"
     * Envía notificación inmediata a jefe de mantenimiento
   - Si tipo = "falta de insumo crítico":
     * Genera ticket de compras con prioridad "urgente"
     * Envía notificación inmediata a jefe de compras
   - Si impacto = "total":
     * Cambia estado de la tarea a "bloqueada"
     * Notifica al jefe
     * El período de pausa comienza automáticamente
8. El sistema muestra: "Incidencia registrada y reportada. ID: [INC-20260301-001]"

**Flujos Alternos:**
- **Sin Foto:** Si es máquina pero no adjunta foto, solicita foto obligatoriamente
- **Incidencia Duplicada:** Si intenta registrar la misma incidencia en menos de 30 minutos, bloquea
- **Sin Permisos:** Si el empleado intenta reportar sin tarea activa, bloquea la acción

**Postcondiciones:**
1. La incidencia está registrada con ID único
2. Si es de máquina o insumo crítico, se generó ticket correspondiente
3. Los roles responsables fueron notificados dentro de 60 segundos
4. Si el impacto es total, la tarea está bloqueada
5. Todo está trazado en el historial

---

## CU-029: MÓDULO DE NOTIFICACIONES
**Actor(es):** Usuario Autenticado de Cualquier Rol
**Descripción:** Permite al sistema generar y gestionar notificaciones centralizadas para cada usuario.

**Precondiciones:**
1. El usuario ha iniciado sesión
2. El sistema ha generado eventos que requieren notificación
3. El módulo de notificaciones está operativo

**Flujo Principal:**
1. El usuario accede al panel
2. Visualiza en la esquina superior derecha contador de notificaciones pendientes
3. El usuario hace clic en el ícono de notificaciones
4. El sistema abre panel lateral con:
   - Lista de notificaciones ordenadas cronológicamente (más recientes primero)
   - Cada notificación muestra:
     * Tipo (ícono + label): "Pedido", "Tarea", "Incidencia", "Alerta Inventario", etc.
     * Título descriptivo
     * Breve descripción del evento
     * Fecha y hora relativa ("hace 5 minutos", "hace 2 horas")
     * Estado visual: "no leída" (resaltada) o "leída" (opaca)
5. El usuario puede hacer clic en una notificación para:
   - Marcarla como "leída"
   - Visualizar los detalles completos
   - Acceder directamente al módulo relacionado (pedido, tarea, etc.)
6. El usuario puede hacer clic en ícono de archivo para:
   - Archivar la notificación
   - La notificación desaparece del panel principal
   - Se mantiene disponible en historial
7. El usuario puede filtrar notificaciones por:
   - Tipo (dropdown): todas, pedidos, tareas, incidencias, etc.
   - Estado (radio buttons): todas, no leídas, archivadas
8. Si hay notificaciones no leídas:
   - El sistema muestra badge red con número en el ícono principal
   - El navegador puede mostrar notificación si lo permite

**Flujos Alternos:**
- **Sin Notificaciones:** Si no hay notificaciones, muestra: "No tienes notificaciones"
- **Notificación Expirada:** Si la notificación tiene más de 30 días, se archiva automáticamente
- **Error al Acceder:** Si hace clic en notificación pero el módulo no existe, muestra: "El elemento ya no está disponible"

**Postcondiciones:**
1. El usuario visualizó todas sus notificaciones relevantes
2. Marcó algunas como leídas
3. Archivó algunas
4. Accedió a módulos desde las notificaciones
5. Todos los eventos de notificación quedan registrados

---

## CU-030: ALERTAS AL JEFE SOBRE PEDIDOS, TAREAS E INVENTARIO
**Actor(es):** Jefe, Gerente de Fábrica
**Descripción:** El sistema genera alertas automáticas de prioridad alta dirigidas al jefe sobre eventos críticos.

**Precondiciones:**
1. El jefe ha iniciado sesión
2. Se han configurado umbrales de alerta
3. Ha ocurrido un evento crítico

**Flujo Principal:**
1. Ocurre un evento que dispara alerta (ej: pedido sin stock, tarea bloqueada, insumo bajo)
2. El sistema genera alerta automática en menos de 60 segundos
3. La alerta aparece en banner prominente en la parte superior del panel del jefe:
   - Ícono rojo de alerta
   - Título descriptivo del evento
   - Descripción breve del problema
   - Prioridad (crítica, urgente, alta, media, baja)
   - Fecha y hora
   - Botón "Acuse de Recibo" (obligatorio)
   - Botón "Ver Detalles"
4. El sistema también:
   - Envía notificación in-app inmediata
   - Envía correo electrónico si está habilitado
   - Muestra contador de alertas pendientes
5. El jefe lee la alerta
6. El jefe puede hacer clic en "Ver Detalles":
   - Se abre panel con información completa
   - Muestra el módulo afectado
   - Proporciona enlace directo para intervenir
7. El jefe hace clic en "Acuse de Recibo":
   - La alerta desaparece del banner
   - Cambia a estado "reconocida"
   - Se traslada a panel de historial de alertas
   - El sistema registra fecha y hora del acuse
8. El jefe puede acceder a "Panel de Control de Alertas":
   - Abre vista con todas las alertas (activas e históricas)
   - Permite filtrar por:
     * Estado: activas, reconocidas, resueltas
     * Tipo: pedidos, tareas, inventario, calidad
     * Prioridad
     * Fecha
   - Muestra para cada alerta:
     * Tipo y descripción
     * Estado actual
     * Fecha de generación
     * Acciones disponibles

**Tipos de Alertas Automáticas:**
- Nuevo pedido sin stock disponible (prioridad: media)
- Tarea bloqueada por incidencia (prioridad: alta)
- Falta de insumo crítico (prioridad: urgente)
- Inventario bajo de insumo (prioridad: media)
- Restauración de producto pendiente de aprobación (prioridad: alta)
- Solicitud de reactivación de cuenta pendiente (prioridad: normal)
- Fallo en envío de notificación automática (prioridad: crítica)

**Flujos Alternos:**
- **Múltiples Alertas:** Si ocurren múltiples eventos simultáneamente, muestra todas en orden de prioridad
- **Alerta Resuelta:** Si el jefe resuelve el problema desde el módulo afectado, la alerta se archiva automáticamente

**Postcondiciones:**
1. El jefe fue alertado sobre el evento crítico
2. Tuvo acceso directo al módulo afectado
3. Reconoció o actuó sobre la alerta
4. Todos los eventos están registrados en auditoría

---

## CU-031: REPORTES DE PEDIDOS E INVENTARIO
**Actor(es):** Jefe, Gerente Comercial
**Descripción:** Permite generar reportes consolidados sobre pedidos e inventario.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o gerente comercial
2. El módulo de reportes está disponible
3. Existen datos en el sistema

**Flujo Principal:**
1. El usuario navega a "Reportes" → "Pedidos e Inventario"
2. El sistema presenta formulario de filtros:
   - Tipo de Reporte (dropdown): Pedidos, Inventario, Combinado
   - Rango de Fechas (date picker): from, to
   - Estado del Pedido (checkbox múltiple): pendiente, aprobado, en producción, entregado, cancelado
   - Referencia del Producto (field opcional)
   - Cliente (dropdown opcional)
   - Combinación Específica (talla-color, opcional)
3. El usuario selecciona los filtros deseados
4. El usuario hace clic en "Generar Reporte"
5. El sistema:
   - Valida los filtros
   - Consulta la base de datos (máximo 60 segundos para 1 año de datos)
   - Consolida los datos
6. Si es reporte "Pedidos":
   - Muestra tabla con columnas:
     * Número de Pedido
     * Cliente Mayorista
     * Fecha de Registro
     * Estado Actual
     * Productos Solicitados
     * Cantidades Totales
     * Combinaciones (talla-color)
     * Ruta (Entrega Directa / Pendiente Fabricación)
   - Muestra totalizadores por estado
7. Si es reporte "Inventario":
   - Muestra tabla con columnas:
     * Referencia
     * Marca
     * Estilo
     * Talla
     * Color
     * Cantidad Disponible
     * Cantidad Reservada
     * Cantidad en Restauración
     * Stock Total
   - Muestra totalizadores por referencia
   - Muestra valor total de inventario
8. Si es reporte "Combinado":
   - Muestra ambas tablas
   - Correlaciona disponibilidad vs. pedidos pendientes
9. El reporte incluye:
   - Fecha de generación
   - Usuario responsable
   - Parámetros de filtro aplicados
   - Hora de ejecución
10. El usuario puede:
    - Visualizar en pantalla
    - Exportar a PDF: formato profesional con gráficos
    - Exportar a Excel: formato tabular para análisis

**Flujos Alternos:**
- **Sin Datos:** Si no hay datos que coincidan con los filtros, muestra: "No hay datos que coincidan con los criterios"
- **Tiempo Excedido:** Si la generación tarda más de 60 segundos, muestra advertencia y permite refinamiento de filtros
- **Exportación Fallida:** Si la exportación falla, muestra error y permite reintentar

**Postcondiciones:**
1. El reporte ha sido generado correctamente
2. Los datos son inmutables (no se pueden editar)
3. Se puede consultar y descargar
4. Se registra el evento en auditoría

---

## CU-032: REPORTES SOBRE TAREAS ASIGNADAS A EMPLEADOS
**Actor(es):** Jefe, Recursos Humanos
**Descripción:** Permite generar reportes sobre el desempeño de tareas asignadas a empleados.

**Precondiciones:**
1. El usuario ha iniciado sesión con rol de jefe o RH
2. El módulo de reportes está disponible
3. Existen tareas asignadas en el sistema

**Flujo Principal:**
1. El usuario navega a "Reportes" → "Tareas de Empleados"
2. El sistema presenta formulario de filtros:
   - Empleado (dropdown: todos o específico)
   - Estado de Tarea (checkbox): pendiente, en progreso, completada, cancelada
   - Tipo de Tarea (dropdown)
   - Prioridad (checkbox): alta, normal, baja
   - Rango de Fechas de Asignación (date picker)
   - Rango de Fechas de Finalización (date picker)
3. El usuario selecciona filtros
4. El usuario hace clic en "Generar Reporte"
5. El sistema genera reporte con tabla que incluye:
   - Nombre Empleado
   - Número de Tarea
   - Título de Tarea
   - Tipo
   - Prioridad
   - Fecha de Asignación
   - Fecha Límite
   - Estado Actual
   - Porcentaje de Avance
   - Tiempo Estándar (horas)
   - Tiempo Real (horas)
   - Eficiencia % (T.Estándar / T.Real * 100)
   - Incidencias Reportadas (cantidad)
   - Observaciones Técnicas
6. El reporte calcula y muestra métricas por empleado:
   - Total de tareas asignadas
   - Total completadas
   - Total en progreso
   - Tasa de retrabajo = (tareas reabierta / tareas completadas) * 100
   - Calificación de Eficiencia: promedio ponderado por empleado
   - Desviación en Horas-Hombre = Σ(Tiempo Real - Tiempo Estándar)
7. El reporte incluye resumen ejecutivo por empleado
8. El usuario puede exportar a PDF o Excel

**Flujos Alternos:**
- **Empleado sin Tareas:** Si el empleado no tiene tareas, muestra valor cero
- **Sin Permisos:** Si empleado intenta acceder, deniega con mensaje de permisos

**Postcondiciones:**
1. El reporte ha sido generado correctamente
2. Muestra desempeño individual por empleado
3. Se puede consultar y descargar
4. Se registra en auditoría

---

## CU-033: SUMA DE PRODUCCIÓN POR EMPLEADO
**Actor(es):** Sistema, Jefe
**Descripción:** El sistema registra automáticamente la producción de cada empleado vinculada a tareas completadas.

**Precondiciones:**
1. Un empleado ha completado una tarea
2. La tarea ha sido aprobada por el supervisor
3. El sistema de suma está operativo

**Flujo Principal:**
1. Un empleado completa una tarea y la marca como "completada"
2. El supervisor revisa y aprueba la tarea
3. El sistema automáticamente:
   - Valida que la tarea esté en estado "completada"
   - Extrae los datos de producción:
     * Número de tarea
     * Nombre del empleado
     * Referencia del producto
     * Combinación talla-color
     * Tipo de intervención
     * Cantidad procesada
     * Fecha de ejecución
     * Resultado operativo
   - Verifica que no haya sido sumada previamente (evita duplicidad)
   - Crea registro de suma en tabla de producción
4. Si la tarea no está cerrada o no tiene cantidades:
   - No se suma
   - El sistema registra el motivo
5. El jefe puede acceder a "Suma de Producción":
   - Navega a "Reportes" → "Suma de Producción"
   - Ve tabla con todos los registros de producción:
     * Nombre Empleado
     * Número Tarea
     * Referencia Producto
     * Talla-Color
     * Tipo de Intervención
     * Cantidad Procesada
     * Fecha
     * Resultado
6. El jefe puede aplicar filtros:
   - Por Empleado
   - Por Fecha
   - Por Tipo de Intervención
   - Por Referencia
7. El reporte calcula totales:
   - Total de pares por empleado
   - Total por tipo de intervención
   - Total por período
   - Promedio de pares por hora

**Flujos Alternos:**
- **Tarea sin Cantidades:** Si no se registraron cantidades, no se suma
- **Tarea Duplicada:** Si intenta sumarse nuevamente, detecta y bloquea

**Postcondiciones:**
1. La producción del empleado está registrada automáticamente
2. Se puede consultar en cualquier momento
3. No hay manipulación manual de datos
4. Todo está trazado en el historial

---

## CU-034: SUMA DE PARES FABRICADOS SEMANALMENTE
**Actor(es):** Sistema, Jefe
**Descripción:** El sistema genera automáticamente cada domingo el consolidado semanal de pares fabricados.

**Precondiciones:**
1. Ha finalizado una semana laboral
2. Se han completado tareas de fabricación
3. Es domingo 23:59 (servidor)

**Flujo Principal:**
1. El servidor detecta que es domingo 23:59
2. El sistema ejecuta procedimiento de consolidación semanal:
   - Consulta todas las tareas de fabricación marcadas como "completadas" en los últimos 7 días
   - Para cada tarea aprobada, extrae:
     * Referencia del producto
     * Talla
     * Color
     * Estilo
     * Marca
     * Cantidad fabricada
     * Empleado responsable
     * Estado final (ingresado a bodega, en cuarentena, pérdida)
3. El sistema agrupa por:
   - Modelo/Referencia
   - Categoría
   - Estado Final
4. El sistema valida conciliación:
   - Pares Producidos = Pares en Inventario + Pares en Cuarentena + Pares Pérdida
   - Si conciliación = OK, procede
   - Si conciliación ≠ OK, genera alerta al jefe con discrepancias
5. El sistema genera registro semanal incluyendo:
   - Semana calendario (ej: Semana 9 de 2026)
   - Fecha de corte (domingo fecha)
   - Total pares fabricados
   - Desglose por modelo
   - Desglose por categoría
   - Desglose por estado final
   - Empleados que participaron
6. El sistema envía correo al jefe con resumen ejecutivo
7. El reporte se guarda y queda disponible para consulta
8. Si una semana no tiene producción, genera corte con valor cero

**Flujos Alternos:**
- **Sin Producción:** Si no hay tareas completadas, genera corte con cero y registra
- **Discrepancias:** Si hay diferencia en la conciliación, genera alerta pero crea el corte

**Postcondiciones:**
1. El consolidado semanal está creado automáticamente
2. Se ha validado la conciliación
3. El jefe ha sido notificado
4. Se puede consultar el corte histórico

---

## CU-035: SUMA DE PARES TOTALES PEDIDOS POR CLIENTE MENSUALMENTE
**Actor(es):** Sistema, Jefe, Gerente Comercial
**Descripción:** El sistema genera automáticamente cada mes el consolidado de pares solicitados por cada cliente.

**Precondiciones:**
1. Ha finalizado un mes calendario
2. Existen pedidos registrados en el mes
3. Es último día del mes 23:59 (servidor)

**Flujo Principal:**
1. El servidor detecta que es último día del mes 23:59
2. El sistema ejecuta procedimiento de consolidación mensual:
   - Consulta todos los pedidos registrados durante ese mes
   - Excluye pedidos en estado "borrador" o "cancelados"
   - Para cada pedido válido, extrae:
     * Número de pedido
     * Nombre cliente mayorista
     * Referencia del producto
     * Talla
     * Color
     * Estilo
     * Marca
     * Cantidad solicitada
     * Estado del pedido
     * Cantidad entregada (si aplica)
     * Fecha prometida de entrega
     * Fecha real de entrega (si se entregó)
3. El sistema valida datos:
   - Si hay combinaciones incompletas o cantidades nulas, excluye el pedido y registra motivo
4. El sistema agrupa por:
   - Cliente mayorista
   - Referencia de producto
   - Estado del pedido
5. El sistema calcula métricas por cliente:
   - Total pares solicitados
   - Total pares entregados
   - Porcentaje cumplimiento entrega = (Pares Entregados / Pares Solicitados) * 100
   - Porcentaje entrega a tiempo = (Pedidos Entregados a Tiempo / Total Pedidos) * 100
6. El sistema genera registro mensual incluyendo:
   - Mes calendario (ej: Marzo 2026)
   - Fecha de corte (último día del mes)
   - Para cada cliente:
     * Nombre cliente
     * Total pares solicitados
     * Total pares entregados
     * % Cumplimiento
     * % Entrega a Tiempo
     * Desglose por referencia
7. El sistema envía correo al jefe y gerente comercial con resumen
8. El reporte se guarda para consulta histórica
9. Si un cliente no tiene pedidos ese mes, genera corte con cero

**Flujos Alternos:**
- **Sin Pedidos:** Si no hay pedidos en el mes, genera consolidado con cero
- **Datos Inconsistentes:** Si hay pedidos con datos incompletos, los excluye y registra

**Postcondiciones:**
1. El consolidado mensual está creado automáticamente
2. Incluye métricas de cumplimiento de entrega
3. El jefe fue notificado
4. Se puede consultar el historial mensual por cliente

