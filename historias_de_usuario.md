# HISTORIAS DE USUARIO
## Sistema de Gestión y Producción de Calzado

---

## HU-001: CREACIÓN DE CUENTAS DE ACCESO
**Prioridad:** Alta

Como cliente potencial,
Quiero crear una solicitud de cuenta de acceso de forma autónoma,
Para acceder al sistema y realizar pedidos de calzado.

**Criterios de Aceptación:**
- Puedo acceder al formulario de registro desde la página principal sin autenticarme
- El formulario solicita: nombre completo, correo, documento, teléfono, razón social y NIT
- El sistema valida que el correo, documento y NIT sean únicos en tiempo real
- Si algún dato está duplicado, se bloquea el registro con mensaje de error
- Puedo ver un acuse de recibo visual y recibir confirmación por correo
- Mi solicitud queda con estado "pendiente de validación"
- El jefe recibe una notificación en menos de 30 segundos
- Se registra toda la actividad en el historial de auditoría

---

## HU-002: VALIDACIÓN Y ACTIVACIÓN DE CUENTAS POR JEFE
**Prioridad:** Alta

Como jefe del sistema,
Quiero revisar y activar las solicitudes de cuentas pendientes,
Para controlar quién accede al sistema y garantizar la seguridad.

**Criterios de Aceptación:**
- Veo una lista de solicitudes pendientes con todos los datos del cliente
- Puedo aprobar o rechazar cada solicitud
- Si rechazo, debo ingresar un comentario obligatorio
- Al aprobar, se genera una contraseña temporal segura (10+ caracteres con mayúscula, minúscula, número y símbolo)
- El cliente recibe un correo con sus credenciales y un enlace válido por 24 horas
- La cuenta cambia a estado "activa"
- Todos los eventos quedan registrados en auditoría

---

## HU-003: INICIO DE SESIÓN
**Prioridad:** Alta

Como usuario registrado,
Quiero acceder al sistema con mis credenciales,
Para ingresar a mi panel y realizar mis actividades.

**Criterios de Aceptación:**
- Puedo iniciar sesión con correo y contraseña
- Si son incorrectos, recibo mensaje específico de error
- Tras 3 intentos fallidos la cuenta se bloquea 30 minutos
- Mi sesión caduca tras 20 minutos de inactividad
- Soy redirigido al panel correspondiente a mi rol
- Todos los intentos (exitosos y fallidos) quedan registrados en auditoría

---

## HU-004: RECUPERACIÓN DE CUENTAS
**Prioridad:** Alta

Como usuario registrado,
Quiero recuperar mi acceso si olvido mi contraseña,
Para poder ingresar nuevamente al sistema de forma segura.

**Criterios de Aceptación:**
- Puedo iniciar el proceso de recuperación ingresando mi correo
- Recibo un enlace seguro válido por 60 minutos
- Puedo establecer una nueva contraseña con requisitos: 10+ caracteres, mayúscula, minúscula, número y símbolo
- Mi contraseña anterior queda invalidada
- Recibo confirmación: "Su contraseña ha sido actualizada exitosamente"
- Todos los eventos quedan registrados en auditoría

---

## HU-005: SOLICITUD DE REACTIVACIÓN DE CUENTAS
**Prioridad:** Alta

Como usuario con cuenta suspendida,
Quiero solicitar la reactivación de mi cuenta,
Para volver a acceder al sistema.

**Criterios de Aceptación:**
- Solo puedo acceder al formulario si mi cuenta está suspendida o inactiva
- El formulario solicita: correo, motivo detallado, documento, teléfono y evidencia opcional
- El sistema genera un ticket con ID único para el jefe
- El jefe puede aprobar o rechazar con comentario obligatorio
- Si se aprueba, mi cuenta cambia a "activa" y recibo notificación
- Si se rechaza, recibo el motivo de la decisión
- Todos los eventos quedan registrados en auditoría

---

## HU-006: CREACIÓN DE CATÁLOGO
**Prioridad:** Alta

Como jefe o diseñador de producto,
Quiero registrar productos en el catálogo,
Para ofertar calzado a clientes y empleados.

**Criterios de Aceptación:**
- Puedo acceder al módulo de creación de catálogo
- El formulario solicita: nombre, referencia (única), descripción, imagen, estado, tallas, colores, material, categoría y marca
- La imagen debe ser JPG o PNG y no superar 2MB
- Si la referencia está duplicada, se bloquea el registro
- Si todo es correcto, se registra el producto con mensaje "Producto registrado exitosamente"
- Los productos con estado "inactivo" quedan ocultos
- No puedo eliminar productos con historial de pedidos, solo desactivarlos
- Todos los eventos quedan registrados en auditoría

---

## HU-007: CLASIFICACIÓN POR CATEGORÍAS
**Prioridad:** Alta

Como jefe,
Quiero organizar los productos en categorías,
Para facilitar la navegación y búsqueda de clientes.

**Criterios de Aceptación:**
- Puedo crear nuevas categorías con nombres únicos
- Puedo editar categorías existentes
- No puedo eliminar categorías vinculadas a productos activos
- Si intento eliminar una categoría con productos, veo una ventana emergente listando los dependientes
- Si una categoría está inactiva, sus productos no aparecen en el catálogo público
- Los filtros por categoría responden en menos de 2 segundos
- Todos los cambios quedan registrados en auditoría

---

## HU-008: GESTIÓN DE MARCAS Y ESTILOS
**Prioridad:** Alta

Como jefe,
Quiero registrar marcas y estilos de calzado,
Para organizarlos de forma jerárquica en el catálogo.

**Criterios de Aceptación:**
- Puedo crear marcas con nombre único y obligatorio
- Dentro de cada marca puedo crear estilos con nombre único
- No puedo eliminar marcas o estilos vinculados a productos activos
- Los estilos inactivos no aparecen en catálogo público
- Puedo editar atributos de marcas y estilos existentes
- Si intento eliminar con dependencias, recibo mensaje claro de error
- Todos los cambios quedan registrados en auditoría

---

## HU-009: VISUALIZACIÓN DE CATÁLOGO COMO VISITANTE
**Prioridad:** Alta

Como visitante sin registrar,
Quiero ver el catálogo público de productos,
Para conocer la oferta de calzado sin necesidad de login.

**Criterios de Aceptación:**
- Puedo acceder al catálogo desde la página principal sin autenticarme
- Veo solo productos con estado "activo" y públicos
- Cada producto muestra: imagen, nombre, referencia, tallas, colores, material, marca y estilo
- No veo precios, costos ni información de inventario
- Puedo aplicar filtros básicos por categoría, marca, estilo, talla y color
- El tiempo de carga y filtros es menor a 3 segundos
- Si no hay productos muestro "No hay productos disponibles en este momento"
- Si intento acceder a funciones restringidas, me redirigen al registro

---

## HU-010: CONSULTA DE CATÁLOGO POR CLIENTE MAYORISTA
**Prioridad:** Alta

Como cliente mayorista autenticado,
Quiero consultar el catálogo completo de productos,
Para preparar mis pedidos basándome en la oferta disponible.

**Criterios de Aceptación:**
- Solo accedo tras iniciar sesión exitosamente
- Veo todos los productos activos con información detallada
- Si una combinación está agotada, aparece deshabilitada
- Puedo guardar productos como favoritos y persistir al cerrar sesión
- Puedo iniciar un proceso de pedido seleccionando modelos
- Las combinaciones disponibles se transfieren automáticamente al formulario de pedido
- Si intento acceder sin autenticación, me redirigen a login
- Todos los eventos de visualización quedan registrados

---

## HU-011: SISTEMA DE FILTRADO DE BÚSQUEDA
**Prioridad:** Alta

Como usuario de cualquier rol,
Quiero filtrar productos por múltiples atributos,
Para encontrar rápidamente lo que busco en el catálogo.

**Criterios de Aceptación:**
- Puedo aplicar filtros compuestos simultáneamente (categoría, marca, estilo, talla, color)
- Los resultados se actualizan en tiempo real sin recargar la página
- El tiempo de respuesta es menor a 2 segundos incluso con 5,000+ modelos
- Puedo buscar por texto libre con coincidencias parciales
- Hay un botón para limpiar todos los filtros y restaurar la vista general
- Si no hay coincidencias muestro "No se encontraron productos"
- Si los filtros son incompatibles, se impide la búsqueda
- Todos los eventos se registran para análisis

---

## HU-012: REALIZACIÓN DE PEDIDOS POR CLIENTE MAYORISTA
**Prioridad:** Alta

Como cliente mayorista,
Quiero registrar un pedido seleccionando productos,
Para solicitar fabricación o entrega inmediata de calzado.

**Criterios de Aceptación:**
- Puedo seleccionar uno o más productos del catálogo
- Para cada producto defino talla, color y cantidad
- Si la cantidad es menor a la mínima configurable, se bloquea con mensaje
- El sistema verifica automáticamente disponibilidad en bodega
- Si hay stock, se marca como "aprobado para entrega"
- Si no hay stock, se marca como "pendiente de fabricación"
- Se genera un número de pedido único
- Mi pedido queda en estado "pendiente de revisión administrativa"
- Recibo notificación de registro exitoso

---

## HU-013: NOTIFICACIÓN DE NUEVOS PEDIDOS
**Prioridad:** Alta

Como jefe, gerente comercial o planificador de producción,
Quiero recibir notificaciones automáticas cuando se registran pedidos,
Para actuar rápidamente en la gestión de entrega o producción.

**Criterios de Aceptación:**
- Recibo una notificación en panel en menos de 5 segundos tras un nuevo pedido
- La notificación incluye: ID del pedido, nombre del cliente, fecha/hora, cantidad, combinaciones y estado
- Puedo ver si será atendido por entrega directa o requiere producción
- Recibo un correo de prioridad alta si está habilitado
- El enlace de la notificación me lleva directamente a la ficha del pedido
- Se evita duplicidad de notificaciones
- Todas las notificaciones quedan trazadas en el historial

---

## HU-014: CONSULTA DE ESTADO DE PEDIDOS POR CLIENTE
**Prioridad:** Alta

Como cliente mayorista,
Quiero consultar el estado de mis pedidos,
Para hacer seguimiento y conocer el progreso de mis solicitudes.

**Criterios de Aceptación:**
- Solo puedo ver mis propios pedidos tras iniciar sesión
- Veo número, fecha, estado, productos, tallas, colores, cantidades y ruta
- Puedo aplicar filtros por estado, fecha o referencia
- El porcentaje de avance de producción se actualiza con latencia máxima de 5 minutos
- Se muestran alertas automáticas de retraso si aplica
- Si intento acceder a pedidos de otro cliente, veo "acceso no autorizado" y se registra
- Si no hay coincidencias con los filtros, recibo un mensaje apropiado
- Todos los eventos se registran en el historial

---

## HU-015: ACTUALIZACIÓN DE ESTADO DE PEDIDOS POR JEFE
**Prioridad:** Alta

Como jefe o gerente comercial,
Quiero modificar el estado de los pedidos,
Para mantener el flujo de trabajo actualizado y coordinar acciones operativas.

**Criterios de Aceptación:**
- Puedo mover pedidos entre estados válidos: pendiente, aprobado, en producción, completado, cancelado, entregado
- El sistema valida que la transición sea coherente
- Es obligatorio registrar el motivo del cambio
- Si cambio a "aprobado", se genera automáticamente una orden de producción
- Los insumos se marcan automáticamente como "reservados" en inventario
- Si cambio a "aprobado para entrega", se verifica disponibilidad en bodega
- Al marcar como "entregado", se verifica que pasó por "aprobado para entrega"
- El historial muestra estado anterior, nuevo estado, fecha y mi nombre
- Todos los cambios están trazados y auditados

---

## HU-016: GESTIÓN DE INVENTARIO DE CALZADO FABRICADO
**Prioridad:** Alta

Como jefe o jefe de bodega,
Quiero registrar, consultar, actualizar y controlar el inventario,
Para reflejar con precisión la disponibilidad real de productos terminados.

**Criterios de Aceptación:**
- Puedo registrar nuevos ingresos especificando referencia, talla, color, cantidad, fecha y origen
- El sistema valida que la referencia exista en el catálogo
- No permite cantidades negativas en ningún SKU
- Cuando se aprueba un pedido para entrega, se descuenta automáticamente el inventario
- Puedo consultar por filtros y realizar ajustes manuales registrando el motivo
- Si intento vender más de lo disponible, muestro "El stock es insuficiente" y no efectúo cambios
- Se genera alerta al jefe de compras si baja de la cantidad mínima
- Todos los movimientos quedan trazados en el historial

---

## HU-017: ACTUALIZACIÓN AUTOMÁTICA DEL INVENTARIO
**Prioridad:** Alta

Como sistema,
Quiero actualizar automáticamente el inventario cuando termina la producción,
Para mantener la integridad y disponibilidad actualizada.

**Criterios de Aceptación:**
- Al marcar una orden como "fabricada" y "aprobada por calidad", se actualiza automáticamente el inventario
- Si la combinación existe, aumenta la cantidad disponible
- Si no existe, crea un nuevo registro con estado "disponible"
- No permite intervención manual en la cantidad de entrada
- Si hay discrepancia mayor al 2%, genera una incidencia de prioridad alta para el jefe
- Se evita duplicidad de ingreso
- Se registra automáticamente fecha, número de pedido y origen "producción interna"
- El registro queda etiquetado con el ID de la orden de producción

---

## HU-018: REGISTRO DE VENTAS Y DESCUENTO EN INVENTARIO
**Prioridad:** Alta

Como jefe o personal comercial,
Quiero registrar ventas manuales de calzado,
Para descontar automáticamente del inventario y mantenerlo actualizado.

**Criterios de Aceptación:**
- Puedo registrar ventas de productos fabricados
- El sistema valida stock en tiempo real antes de procesar
- Si el stock es insuficiente, se rechaza la venta
- El formulario solicita: referencia, talla, color, cantidad, fecha, destino, motivo, cliente y responsable
- Cuando confirmo, se descuenta automáticamente del inventario
- Se registra como "salida por venta directa"
- Si intento duplicar una venta, se bloquea la acción
- Todos los eventos quedan trazados en el historial

---

## HU-019: REGISTRO DE PÉRDIDAS POR CALZADO DEFECTUOSO
**Prioridad:** Alta

Como jefe o personal de control de calidad,
Quiero registrar pérdidas de inventario por defectos,
Para mantener el stock real y documentar descartes.

**Criterios de Aceptación:**
- Puedo registrar pérdidas seleccionando referencia, talla, color, cantidad, fecha y motivo
- Debo seleccionar un código de defecto de una lista predefinida
- Si soy operario, la pérdida queda "pendiente de aprobación de calidad"
- Si soy jefe de calidad, la aprobación es inmediata
- El inventario de "productos terminados" se reduce
- El inventario de "scrap stock" aumenta con referencia al defecto
- No puedo registrar si la cantidad excede el stock disponible
- Si intento duplicar se bloquea la acción
- Todos los eventos quedan registrados en auditoría

---

## HU-020: PROCESO DE RESTAURACIÓN DE CALZADO DEFECTUOSO
**Prioridad:** Alta

Como jefe o jefe de calidad,
Quiero restaurar calzado defectuoso previamente descartado,
Para recuperar unidades y reincorporarlas al inventario.

**Criterios de Aceptación:**
- Puedo seleccionar productos defectuosos del historial de pérdidas
- Especifico referencia, talla, color, cantidad, tipo de defecto e intervención aplicada
- La cantidad a restaurar no puede exceder la cantidad descartada
- Durante el proceso, las unidades se marcan como "en restauración" y se bloquean
- Solo el jefe de calidad autoriza el cambio final a "restaurado"
- Puedo marcar como "aprobadas" para reincorporar automáticamente al inventario
- O como "rechazadas" para mantenerlas como pérdida definitiva
- Se registra el costo de reparación
- Todos los eventos quedan trazados en el historial de restauración

---

## HU-021: CREACIÓN DE TAREAS POR EL JEFE
**Prioridad:** Alta

Como jefe o planificador de producción,
Quiero crear tareas operativas dentro del sistema,
Para organizar el trabajo, distribuir responsabilidades y documentar actividades.

**Criterios de Aceptación:**
- Puedo acceder al módulo de creación de tareas
- Los campos obligatorios son: título, descripción, tipo, prioridad, fecha límite y referencia a orden de producción
- El tiempo estándar se valida automáticamente desde la ficha del modelo (no editable)
- Se impide crear tareas duplicadas para la misma orden y proceso
- Una vez registrada, aparece en el panel del empleado con estado "pendiente de inicio"
- El empleado asignado recibe notificación inmediata
- Si asigno a usuario inactivo, se muestra mensaje de error
- Todos los eventos quedan registrados en auditoría

---

## HU-022: ASIGNACIÓN DE TAREAS A EMPLEADOS
**Prioridad:** Alta

Como jefe o planificador de producción,
Quiero asignar tareas a empleados,
Para distribuir eficientemente el trabajo.

**Criterios de Aceptación:**
- Puedo seleccionar una tarea en estado "pendiente"
- La asigno a un empleado con cuenta activa y rol autorizado
- El sistema advierte si la asignación excede el 110% de capacidad, pero permite continuar
- Se registra fecha de asignación, nombre del empleado y se activa visualización en su panel
- Es obligatorio establecer fecha límite
- La tarea cambia a estado "asignada" y se bloquea para otros usuarios excepto yo
- El empleado recibe notificación in-app y por correo
- Solo puedo reasignar si está en estado "asignada" o "en progreso"
- Todos los eventos quedan trazados

---

## HU-023: CONSULTA DE TAREAS ASIGNADAS POR EL EMPLEADO
**Prioridad:** Alta

Como empleado,
Quiero consultar las tareas asignadas,
Para conocer mis responsabilidades y organizar mi carga de trabajo.

**Criterios de Aceptación:**
- Solo veo mis propias tareas tras iniciar sesión
- Veo todas clasificadas por estado: pendiente, en progreso, completada, cancelada
- Cada tarea muestra: título, descripción, tipo, prioridad, fechas, estado y observaciones del jefe
- Puedo aplicar filtros por prioridad o fecha límite
- Veo un indicador de tiempo restante hasta la fecha límite
- Las tareas completadas permanecen visibles pero no puedo editarlas
- Si no tengo tareas, veo un mensaje apropiado
- Si aplico filtros sin coincidencias, recibo un mensaje
- Todos los eventos se registran

---

## HU-024: REPORTE DE AVANCES E INCIDENCIAS EN TAREAS
**Prioridad:** Alta

Como empleado,
Quiero registrar avances parciales e incidencias durante la ejecución de tareas,
Para mantener informado al jefe sobre el progreso y desvíos.

**Criterios de Aceptación:**
- Puedo registrar inicio con check-in y marcas de tiempo del servidor
- Puedo pausar y calcular el tiempo real invertido
- El sistema valida que no inicie dos tareas simultáneamente
- Puedo registrar: porcentaje de avance, descripción, tipo de incidencia, observaciones y archivos
- Si reporto incidencia crítica, genera notificación al mantenimiento o comprador en menos de 60 segundos
- Si el avance es 100%, debo confirmar antes de marcar como completada
- Si reporto incidencia crítica, la tarea se marca como "bloqueada" automáticamente
- Debo justificar pausa si supera el 10% del tiempo estándar
- Se calcula eficiencia como tiempo estándar / tiempo real
- Todos los reportes quedan vinculados a la tarea y registrados

---

## HU-025: CONFIRMACIÓN DE FINALIZACIÓN DE TAREAS
**Prioridad:** Alta

Como empleado,
Quiero confirmar la finalización de una tarea,
Para cerrar formalmente el ciclo y notificar al jefe.

**Criterios de Aceptación:**
- Puedo acceder al módulo de confirmación desde una tarea en estado "asignada" o "en progreso"
- Registro la cantidad exacta de pares procesados
- Puedo adjuntar evidencia fotográfica e informe final
- Es obligatorio: resumen del trabajo realizado, fecha de finalización, observaciones opcionales
- Si la cantidad reportada difiere más del 1% de la asignada, debo ingresar clave de supervisor
- La diferencia se etiqueta como "desperdicio" o "falta"
- La tarea cambia a estado "completada" y se bloquea para edición
- El jefe recibe notificación automática
- Si es la última tarea de la orden, se genera alerta a control de calidad
- Todos los eventos quedan trazados

---

## HU-026: NOTIFICACIÓN AL JEFE DE TAREAS FINALIZADAS
**Prioridad:** Alta

Como jefe o supervisor de línea,
Quiero recibir notificaciones cuando se finalizan tareas,
Para revisar el trabajo y tomar decisiones de aprobación o retrabajo.

**Criterios de Aceptación:**
- Recibo notificación automática en tiempo real cuando un empleado marca tarea como completada
- La notificación incluye: número, título, tipo, prioridad, fechas, empleado, resumen, eficiencia y observaciones
- Aparece en mi panel con todos los datos relevantes
- Hay botones de "aprobar" o "rechazar"
- Puedo ver la eficiencia calculada y enlace directo a evidencias
- Si rechazo con motivo específico, la tarea pasa a estado "reabierta"
- El empleado recibe notificación del rechazo y comienza retrabajo en cero
- Los eventos quedan trazados sin duplicidades

---

## HU-027: MODIFICACIÓN Y ELIMINACIÓN DE TAREAS POR EL JEFE
**Prioridad:** Alta

Como jefe o planificador,
Quiero editar o eliminar tareas,
Para corregir errores o ajustar cambios en la planificación.

**Criterios de Aceptación:**
- Puedo editar tareas si están en estado "pendiente" o "asignada"
- No puedo editar tareas "completadas" o "canceladas"
- Puedo actualizar: título, descripción, tipo, prioridad, fecha límite, empleado asignado y observaciones
- Si intento eliminar una tarea sin actividad registrada, me pide confirmación explícita
- Si tiene registro de tiempo, solo puedo "cancelar" con motivo obligatorio
- Una vez cancelada, queda registrada con el motivo
- Desaparece del panel del empleado tras eliminación
- El historial muestra cambios de fecha anterior a nueva con mi ID
- Todos los cambios quedan en auditoría con valores anteriores y nuevos

---

## HU-028: REGISTRO DE INCIDENCIAS DE MAQUINARIA E INSUMOS
**Prioridad:** Alta

Como empleado con tarea activa,
Quiero reportar incidencias de maquinaria o falta de insumos,
Para documentar obstáculos y facilitar acciones correctivas.

**Criterios de Aceptación:**
- Puedo acceder al módulo solo si tengo una tarea "asignada" o "en progreso"
- El formulario solicita: tipo, descripción, código/nombre del equipo, foto, área, fecha/hora, impacto y evidencia
- Al registrar, se vincula automáticamente a mi tarea activa
- Si el impacto es total, la tarea se marca como "bloqueada"
- Se genera notificación inmediata al jefe
- Incidencia "falta de insumo crítico" genera ticket urgente al jefe de compras
- Se impide registrar duplicadas en intervalo menor a 30 minutos
- Para maquinaria se genera ticket y el jefe de mantenimiento recibe notificación
- Todos los eventos quedan trazados en el historial técnico

---

## HU-029: MÓDULO DE NOTIFICACIONES
**Prioridad:** Alta

Como usuario de cualquier rol,
Quiero tener un centro de notificaciones centralizado,
Para recibir y gestionar información oportuna sobre eventos operativos.

**Criterios de Aceptación:**
- Todas las notificaciones aparecen en mi panel en tiempo real
- Están clasificadas por tipo y prioridad, organizadas cronológicamente
- Veo un contador visible con notificaciones pendientes de leer
- Cada notificación incluye: título, descripción, tipo, prioridad, fecha/hora y enlace directo al módulo
- Puedo marcar como "leída" al acceder
- Puedo "archivar" para ocultar del panel principal
- Recibo solo notificaciones relevantes a mi rol
- Persisten hasta que las marco como leídas
- Puedo filtrar por tipo y prioridad
- Si soy jefe, veo panel de control con todas las generadas, filtrable por tipo, estado, fecha y destinatario

---

## HU-030: ALERTAS AL JEFE SOBRE PEDIDOS, TAREAS E INVENTARIO
**Prioridad:** Alta

Como jefe o gerente de fábrica,
Quiero recibir alertas automáticas sobre eventos críticos,
Para intervenir rápidamente en pedidos, tareas e inventario.

**Criterios de Aceptación:**
- Recibo alertas automáticas cuando hay pedidos que no se pueden aprobar por falta de inventario
- Recibo alertas cuando hay incidencias críticas en maquinaria o insumos
- Recibo alertas cuando el inventario baja de la cantidad mínima configurada
- Recibo alertas cuando se detecta agotamiento de combinaciones específicas
- Las alertas aparecen en tiempo real con contador visible
- Las de prioridad "urgente" aparecen en banner prominente con botón de "acuse de recibo"
- Puedo debo registrar "acuse de recibo" obligatoriamente para alertas urgentes
- Las alertas incluyen título, tipo, prioridad, fecha/hora, descripción y enlace directo
- Tengo panel de control específico para filtrar y revisar todas las alertas

---

## HU-031: REPORTES DE PEDIDOS E INVENTARIO
**Prioridad:** Alta

Como jefe o gerente,
Quiero generar reportes sobre pedidos e inventario,
Para analizar el desempeño operativo y logístico.

**Criterios de Aceptación:**
- Puedo acceder al módulo de reportes tras iniciar sesión
- Puedo generar reportes en tiempo real con filtros por rango de fechas, estado, referencia, cliente y movimiento
- El reporte de pedidos incluye: número, cliente, fecha, estado, productos, cantidades, combinaciones y ruta
- El reporte de inventario incluye: referencia, marca, estilo, talla, color, cantidad disponible, reservada y en restauración
- El tiempo de ejecución es menor a 60 segundos para un año de datos
- Puedo exportar a PDF y Excel con formato legible
- Los datos son inmutables y consistentes con auditoría
- Si no hay resultados, muestro mensaje correspondiente
- Incluya fecha de generación, usuario responsable, parámetros y totalizadores por categoría

---

## HU-032: REPORTES SOBRE TAREAS ASIGNADAS A EMPLEADOS
**Prioridad:** Alta

Como jefe o recursos humanos,
Quiero generar reportes sobre tareas asignadas,
Para analizar el desempeño individual y colectivo.

**Criterios de Aceptación:**
- Solo accedo tras autenticarme con rol de jefe o recursos humanos
- Puedo filtrar por empleado, estado, tipo de tarea, prioridad y fechas
- El reporte incluye: nombre del empleado, número de tarea, título, tipo, prioridad, fechas, estado, porcentaje de avance e incidencias
- Se calcula tasa de retrabajo (tiempo retrabajo / tiempo total)
- Se calcula calificación de eficiencia del empleado
- Es obligatorio filtro por rango de fechas, supervisor y tipo de proceso
- Únicamente muestro tareas registradas y trazadas correctamente
- Puedo exportar a PDF y Excel
- Incluya fecha de generación, usuario responsable, parámetros y totalizadores
- Si un empleado accede, se deniega con "permiso insuficiente"

---

## HU-033: SUMA DE PRODUCCIÓN POR EMPLEADO
**Prioridad:** Alta

Como jefe,
Quiero generar un registro de la producción ejecutada por cada empleado,
Para analizar rendimiento individual y apoyar evaluación y compensación.

**Criterios de Aceptación:**
- El sistema registra automáticamente cada unidad cuando se cierra una tarea "completada"
- Se calcula automáticamente desde confirmaciones de tareas aprobadas
- Para cada registro asocio: número de tarea, empleado, referencia de producto, combinación, tipo de intervención, cantidad, fecha y resultado
- No se suma si la tarea no está cerrada
- No permito manipulación de datos de producción
- El reporte incluye cantidad de pares defectuosos
- Se impide duplicidad de registros
- Puedo consultar la suma por empleado, fecha, intervención o referencia con filtros combinados
- Cada registro queda trazado con fecha, hora, operación, cantidad, empleado responsable y vínculo a tarea
- Si tiene inconsistencias, se impide la suma y muestro mensaje

---

## HU-034: SUMA DE PARES FABRICADOS SEMANALMENTE
**Prioridad:** Alta

Como jefe,
Quiero un consolidado semanal de la producción,
Para analizar productividad y control de cumplimiento por ciclos.

**Criterios de Aceptación:**
- El consolidado se genera automáticamente cada domingo a las 23:59
- Incluye todas las tareas marcadas como "completadas" en los 7 días anteriores
- Se desglose obligatorio por modelo, categoría y estado final
- Asegura conciliación: pares producidos = pares a inventario + en cuarentena + pérdida
- Valida que cada tarea esté cerrada correctamente
- Si hay discrepancias, excluye del corte y muestra motivo
- El reporte incluye: semana calendario, fecha de corte, total de pares, desglose por referencia, talla, color, estilo, marca y empleado
- No permito edición manual pero sí consulta histórica y exportación
- Si una semana no tiene producción, genero el corte con valor cero
- Puedo consultar filtrando por semana, referencia o empleado

---

## HU-035: SUMA DE PARES TOTALES PEDIDOS POR CLIENTE MENSUALMENTE
**Prioridad:** Alta

Como jefe o gerente comercial,
Quiero un consolidado mensual de pedidos por cliente,
Para analizar demanda y apoyar planificación y fidelización.

**Criterios de Aceptación:**
- El consolidado se genera automáticamente el último día del mes a las 23:59
- Incluye todos los pedidos registrados por clientes mayoristas en el período, excepto cancelados y "borrador"
- Se calcula porcentaje de cumplimiento: pares entregados / pares solicitados
- Se calcula porcentaje de entrega a tiempo
- Valida que cada pedido esté correctamente registrado con combinaciones y cantidad
- Si hay inconsistencias, excluye del consolidado y registra motivo
- El reporte incluye: mes calendario, fecha de corte, cliente, total de pares solicitados, desglose por referencia, talla, color, estilo, marca y estado del pedido
- No permito edición manual pero sí consulta histórica y exportación
- Si un cliente no registra pedidos, genero el corte con valor cero
- Puedo consultar filtrando por cliente, mes, estado del pedido o referencia
