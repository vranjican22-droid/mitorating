# Base de conocimiento de la aplicación de pádel de Seba

> **Versión:** 1.0  
> **Fecha:** Mayo 2026  
> **Estado del proyecto:** En producción — versión inicial lanzada  
> **URL de la aplicación:** https://vranjican22-droid.github.io/mitorating/mito-rating.html

---

## 1. Propósito general de la aplicación

### ¿Para qué sirve?

Mito Rating es una plataforma digital que permite a los jugadores de pádel amateur en Argentina medir su nivel de juego con un número objetivo — el **Rating Mito** — que sube o baja según sus resultados reales en cancha.

### ¿Qué problema resuelve?

Hoy en día, los jugadores de pádel en Argentina se auto-declaran en categorías (3ra, 4ta, etc.) sin ningún dato real que lo respalde. Esto genera:
- Torneos desequilibrados donde el nivel de los participantes no coincide.
- Partidos injustos que frustran la experiencia.
- Clubes que no tienen forma de organizar torneos por nivel real.
- Jugadores que no saben cómo están progresando.

### ¿Quiénes la usan?

La aplicación tiene tres tipos de usuarios:

1. **Jugadores** — Se registran, cargan sus partidos y ven su rating, historial y ranking frente a otros jugadores de su ciudad.
2. **Clubes** — Publican torneos, gestionan inscripciones y organizan el cuadro automáticamente por nivel de rating.
3. **Administrador (Seba)** — Tiene acceso total al sistema: puede ver y modificar jugadores, torneos, partidos e ingresos.

### ¿Qué valor aporta?

- A los **jugadores**: saben cuál es su nivel real y pueden encontrar torneos compatibles con ese nivel.
- A los **clubes**: llenan los cupos de sus torneos automáticamente y cobran sin administración manual.
- A **Seba / Mito**: genera ingresos por un fee del 10% sobre cada inscripción a torneos, más suscripciones Premium (Mito Pro).

---

## 2. Perfil del dueño del proyecto

**Seba Vranjican** es el dueño funcional de la aplicación. Es el dueño de la marca **Mito Deportes** y tiene experiencia en el ecosistema del pádel argentino, pero **no es programador**.

### Recomendaciones para trabajar con Seba

- Explicar siempre en lenguaje claro, sin tecnicismos innecesarios.
- Antes de hacer cualquier cambio en el código, explicar qué se va a tocar y por qué.
- Trabajar de a un cambio por vez, nunca varios simultáneos.
- Después de cada cambio, indicar cómo probarlo.
- No romper funcionalidades que ya están funcionando.
- Si algo no está claro, preguntar antes de asumir.
- Registrar cada decisión importante en este documento.

---

## 3. Estado actual de la aplicación

### 3.1 Funcionalidades que ya existen y funcionan

| Funcionalidad | Estado | Cómo probarla |
|---|---|---|
| Registro de usuarios con email y contraseña | ✅ Funcionando | Abrir la app en incógnito y registrarse |
| Login con Google | ✅ Funcionando | Clic en "Continuar con Google" |
| Detección automática de rol (admin vs jugador) | ✅ Funcionando | Ingresar con el email de Seba |
| Perfil del jugador con rating en vivo | ✅ Funcionando | Ingresar como jugador |
| Selector de provincia → ciudad en cascada | ✅ Funcionando | Probar en el formulario de registro |
| Ranking de jugadores (por ciudad y país) | ✅ Funcionando | Sección "Ranking" en la app |
| Panel de administración completo | ✅ Funcionando | Ingresar con el email admin |
| Ver, editar, verificar y eliminar jugadores | ✅ Funcionando | Admin → Jugadores |
| Ver historial de rating de cada jugador | ✅ Funcionando | Admin → Jugadores → Ver |
| Editar rating manualmente | ✅ Funcionando | Admin → Jugadores → Ver |
| Exportar jugadores a CSV | ✅ Funcionando | Admin → Jugadores → Exportar CSV |
| Crear torneos con detalle completo | ✅ Funcionando | Admin → Torneos → + Nuevo torneo |
| Ver inscriptos de un torneo | ✅ Funcionando | Admin → Torneos → Inscriptos |
| Generar cuadro automático por rating | ✅ Funcionando | Admin → Torneos → Cuadro |
| Notificar inscriptos de un torneo | ✅ Funcionando | Admin → Torneos → Notificar |
| Dashboard con KPIs en tiempo real | ✅ Funcionando | Admin → Dashboard |
| Gráfico de crecimiento de jugadores | ✅ Funcionando | Admin → Dashboard |
| Mapa de jugadores por provincia | ✅ Funcionando | Admin → Dashboard |
| Proyección de ingresos | ✅ Funcionando | Admin → Dashboard |
| Registro de partido con cálculo ELO | ✅ Funcionando | App jugador → Partido |
| Base de datos real con Supabase | ✅ Funcionando | Datos persisten entre sesiones |

### 3.2 Funcionalidades incompletas o con problemas conocidos

| Funcionalidad | Problema | Estado |
|---|---|---|
| Google login para usuarios nuevos | Solo acepta "usuarios de prueba" porque la app Google no está publicada en producción | Pendiente: publicar la app en Google Cloud |
| Confirmación de partidos por los 4 jugadores | El sistema envía notificación pero no hay flujo móvil para que los otros 3 confirmen | Pendiente de implementar |
| Tienda Mito | La sección existe visualmente pero no procesa pagos reales | Pendiente |
| Mito Pro con pago real | El botón activa Pro pero no conecta con un procesador de pagos | Pendiente |
| Panel de club desde la app del jugador | Existe pero el formulario de torneo desde la vista jugador no tiene todos los campos | Pendiente de mejorar |

### 3.3 Funcionalidades sensibles que NO deben romperse

Estas partes del sistema son críticas. Cualquier cambio en ellas debe hacerse con mucho cuidado:

1. **El algoritmo ELO de cálculo de rating** — Es la función `confirmar_partido()` en Supabase. Cualquier error aquí afecta los ratings de todos los jugadores.
2. **El sistema de autenticación** — El código que decide si un usuario es admin o jugador depende del email. No cambiar la lista de emails admin sin avisar.
3. **La conexión con Supabase** — Las credenciales (URL y clave anon) están en el archivo HTML. No cambiarlas sin actualizar el archivo en GitHub.
4. **El selector de provincia → ciudad** — La función `PROV_CIUDADES` tiene todas las localidades de Argentina. No sobreescribirla.
5. **El historial de rating** — La tabla `historial_rating` registra cada cambio de puntos. No borrar estos registros.

---

## 4. Reglas del negocio detectadas

### Regla 1: Rating inicial en cero

**Descripción:** Todos los jugadores nuevos arrancan con rating 0, independientemente de su nivel real.

**Ejemplo:** Seba se registra → su rating es 0. Melania se registra → su rating es 0.

**Estado:** Confirmada  
**Fuente:** Código + decisión de Seba en la conversación  
**Observaciones:** Esto puede generar partidos muy desequilibrados al principio hasta que los ratings se calibren con partidos reales.

---

### Regla 2: El rating no tiene techo

**Descripción:** El rating puede subir indefinidamente. No hay un límite máximo.

**Estado:** Confirmada  
**Fuente:** Código (algoritmo ELO sin techo)  
**Observaciones:** Pendiente de confirmar si en el futuro se quiere un sistema de 0 a 10 similar al UTR.

---

### Regla 3: Cálculo ELO para parejas

**Descripción:** El sistema calcula el rating promedio de cada pareja (promedio de los dos jugadores) y aplica el algoritmo ELO entre las dos parejas. El cambio de puntos se distribuye entre los dos jugadores de cada pareja.

**Ejemplo:** 
- Pareja A: jugadores con rating 800 y 600 → promedio 700
- Pareja B: jugadores con rating 400 y 300 → promedio 350
- Si Pareja A gana, gana pocos puntos (era favorita). Si Pareja B gana, gana muchos puntos (era la menos favorita).

**Estado:** Confirmada  
**Fuente:** Función SQL `confirmar_partido()` en Supabase  
**Observaciones:** No modificar esta función sin documentar el cambio primero.

---

### Regla 4: Distribución de puntos por rol

**Descripción:** El jugador de derecha recibe el 55% del cambio de rating y el jugador de revés recibe el 45%.

**Ejemplo:** Si la pareja gana +20 puntos, el de derecha suma +11 y el de revés suma +9.

**Estado:** Confirmada  
**Fuente:** Código en función `confirmar_partido()`  
**Observaciones:** Pendiente de revisar si esta proporción es la correcta o si Seba quiere cambiarla.

---

### Regla 5: Margen de victoria multiplica los puntos

**Descripción:** El margen de games entre los dos sets afecta cuántos puntos se ganan o pierden:
- Margen mayor a 6 games: multiplicador 1.5x (victoria amplia)
- Margen de 4 a 6 games: multiplicador 1.0x (victoria normal)
- Margen menor a 4 games: multiplicador 0.7x (victoria ajustada)

**Ejemplo:** Ganar 6-0/6-0 vale más que ganar 7-5/7-5.

**Estado:** Confirmada  
**Fuente:** Función `calcular_elo()` en Supabase  
**Observaciones:** No modificar sin documentar.

---

### Regla 6: Torneos filtrados por rating compatible

**Descripción:** Cada torneo tiene un rating mínimo y máximo. Los jugadores solo ven como "compatibles" los torneos donde su rating está dentro de ese rango.

**Estado:** Confirmada  
**Fuente:** Código en `loadTorneos()`  
**Observaciones:** Un jugador puede inscribirse en torneos "fuera de nivel" pero se le advierte.

---

### Regla 7: Fee Mito del 10%

**Descripción:** Mito cobra un fee del 10% sobre el precio de cada inscripción. Este fee se suma al precio del torneo y lo paga el jugador.

**Ejemplo:** Torneo de $10.000 → el jugador paga $11.000 ($10.000 + $1.000 de fee).

**Estado:** Confirmada  
**Fuente:** Código en el checkout de inscripción  
**Observaciones:** El fee se registra en la tabla `inscripciones` campo `fee_mito`.

---

### Regla 8: Verificación manual de jugadores

**Descripción:** Los jugadores nuevos quedan con estado "Sin verificar". El administrador puede marcarlos como "Verificados" manualmente desde el panel de admin.

**Estado:** Confirmada  
**Fuente:** Código + conversación  
**Observaciones:** La verificación es solo visual por ahora. No bloquea ni habilita ninguna funcionalidad específica.

---

### Regla 9: Emails admin hardcodeados

**Descripción:** Los emails que tienen acceso al panel de administración están escritos directamente en el código. Actualmente son: `vranjican22@gmail.com` e `info@mitodeportes.com.ar`.

**Estado:** Confirmada  
**Fuente:** Código — variable `ADMIN_EMAILS`  
**Observaciones:** Para agregar un nuevo admin hay que modificar el código y volver a subir el archivo.

---

### Regla 10: Partido requiere confirmación de los 4 jugadores

**Descripción:** Cuando un jugador carga un partido, los otros 3 jugadores deben confirmar el resultado para que el rating se actualice.

**Estado:** Parcialmente implementada  
**Fuente:** Código + base de datos  
**Observaciones:** El sistema guarda el partido como "pendiente" y envía notificaciones, pero el flujo de confirmación desde la app del jugador todavía no está completo. El admin puede confirmar manualmente desde el panel.

---

### Regla 11: Mito Pro con descuentos

**Descripción:** Los jugadores con suscripción Mito Pro activa reciben un 15% de descuento en todas las inscripciones a torneos.

**Estado:** Confirmada  
**Fuente:** Código  
**Observaciones:** El pago de Mito Pro no está conectado a un procesador real todavía.

---

## 5. Sistema de puntuación y ranking

### Cómo funciona el rating

El rating es un número que representa el nivel de juego de un jugador. Empieza en **0** y cambia después de cada partido confirmado.

El sistema usa una adaptación del **algoritmo ELO** (el mismo sistema que usa el ajedrez internacional) modificado para pádel en pareja.

### Qué eventos generan cambios de rating

- **Ganar un partido registrado y confirmado** → el rating sube.
- **Perder un partido registrado y confirmado** → el rating baja.
- Participar en un torneo sin cargar partidos → no cambia el rating.
- El rating inicial (0) no cambia hasta el primer partido confirmado.

### Cuánto cambia el rating

Depende de tres factores:
1. **El nivel de los rivales**: ganarle a jugadores con más rating vale más puntos.
2. **El margen de victoria**: ganar por más games da más puntos.
3. **El rol en cancha**: el de derecha recibe el 55% del cambio, el de revés el 45%.

### Dónde está el cálculo

El cálculo está en la base de datos Supabase, en dos funciones SQL:
- `calcular_elo()` — calcula cuántos puntos cambian según el nivel de los rivales.
- `confirmar_partido()` — aplica el cambio a los 4 jugadores y guarda el historial.

### Ranking

- El ranking es **individual** (por jugador, no por pareja).
- Se ordena de mayor a menor rating.
- Hay vistas por ciudad (Buenos Aires), por país (Argentina) y por club.
- No hay ranking por categoría todavía.
- No hay vencimiento de puntos por tiempo todavía.

### Pendiente de confirmar con Seba

- ¿Querés implementar un sistema de puntos por posición en torneos (tipo UTR donde el rating va de 0 a 10)?
- ¿Los puntos deberían vencer después de 12 meses de inactividad?
- ¿Querés un ranking separado por categoría?
- ¿El rating debería considerar solo los últimos N partidos o todos los históricos?

---

## 6. Datos principales que maneja la aplicación

### Jugador

**Qué representa:** Una persona que juega pádel y usa la plataforma.

**Dónde se usa:** En toda la aplicación — ranking, partidos, inscripciones, torneos.

**Campos importantes:**
- `nombre` — nombre completo
- `email` — también es su usuario de login
- `rating` — su puntuación actual (empieza en 0)
- `rating_provisional` — true/false si ya tiene partidos confirmados
- `rol` — derecha o revés
- `provincia` y `ciudad` — su ubicación
- `verificado` — si el admin lo confirmó como jugador real
- `es_pro` — si tiene suscripción Mito Pro activa
- `partidos_jugados`, `partidos_ganados`, `mejor_racha` — estadísticas

**Relaciones:** Un jugador puede tener muchas inscripciones, muchos partidos y un historial de rating.

---

### Torneo

**Qué representa:** Un torneo de pádel organizado por un club o por Mito.

**Dónde se usa:** En la sección Torneos de la app y en el panel de admin.

**Campos importantes:**
- `nombre` — nombre del torneo
- `fecha_inicio` — cuándo empieza
- `horario` — hora de inicio
- `direccion` — dónde se juega
- `descripcion` — información adicional
- `rating_min` y `rating_max` — rango de nivel aceptado
- `cupos_max` y `cupos_ocupados` — capacidad
- `precio` — costo de inscripción por pareja
- `formato` — Round Robin, Eliminación directa, etc.
- `estado` — borrador / abierto / en_curso / finalizado
- `patrocinado` — si Mito aporta premios

**Relaciones:** Un torneo tiene muchas inscripciones. Un torneo tiene muchos partidos.

---

### Partido

**Qué representa:** Un partido de pádel entre dos parejas, con resultado registrado.

**Dónde se usa:** En la sección "Cargar partido" de la app y en el admin.

**Campos importantes:**
- `jugador1_id`, `jugador2_id` — la pareja A (derecha y revés)
- `jugador3_id`, `jugador4_id` — la pareja B (derecha y revés)
- `set1_a`, `set1_b`, `set2_a`, `set2_b` — resultado de cada set
- `ganador` — "AB" o "CD"
- `confirmado` — si todos los jugadores validaron el resultado
- `delta_j1` a `delta_j4` — cuántos puntos cambió cada jugador

---

### Inscripción

**Qué representa:** El registro de una pareja (o jugador solo) en un torneo.

**Campos importantes:**
- `torneo_id`, `jugador1_id`, `jugador2_id`
- `modo` — con pareja o solo
- `precio_pagado` — lo que pagó
- `fee_mito` — el 10% que se lleva Mito
- `descuento_pro` — si se aplicó descuento Pro
- `estado` — pendiente / confirmada / cancelada

---

### Historial de Rating

**Qué representa:** El registro de cada cambio de puntos de un jugador.

**Campos importantes:**
- `jugador_id`, `partido_id`
- `rating_antes`, `rating_despues`, `delta`

**Observaciones:** Este historial es la fuente de verdad para saber cómo evolucionó el rating de un jugador. No debe borrarse.

---

### Club

**Qué representa:** Un club o sede de pádel que usa la plataforma para organizar torneos.

**Estado actual:** La estructura existe en la base de datos pero la gestión de clubes todavía está básica. Pendiente de desarrollar más.

---

## 7. Arquitectura del sistema (explicada sin tecnicismos)

La aplicación tiene **tres partes**:

### 1. El archivo HTML (la app visual)
- Un único archivo llamado `mito-rating.html`.
- Contiene toda la interfaz: el login, la app del jugador y el panel de admin.
- Está guardado en GitHub y se publica automáticamente en internet.
- **Cualquier cambio en la interfaz requiere modificar este archivo y subirlo a GitHub.**

### 2. La base de datos (Supabase)
- Es donde se guardan todos los datos: jugadores, torneos, partidos, inscripciones, etc.
- Es una base de datos en la nube, accesible desde internet.
- Los cambios en la estructura de la base (agregar columnas, crear tablas) se hacen desde el "SQL Editor" de Supabase.
- **Los datos persisten independientemente del archivo HTML.**

### 3. El sistema de autenticación (Supabase Auth)
- Maneja los logins: email/contraseña y Google.
- Cada usuario tiene una cuenta en Supabase Auth Y un perfil en la tabla `jugadores`.
- Si se elimina a un usuario, hay que eliminarlo de ambos lugares.

### Dónde está todo

| Qué | Dónde |
|---|---|
| La app (interfaz) | https://github.com/vranjican22-droid/mitorating |
| La URL pública | https://vranjican22-droid.github.io/mitorating/mito-rating.html |
| La base de datos | https://supabase.com/dashboard/project/zvgpdvjosjtkqqidykdf |
| El SQL Editor | https://supabase.com/dashboard/project/zvgpdvjosjtkqqidykdf/sql/new |
| Google OAuth | https://console.cloud.google.com/auth/clients?project=verificador-facturas |

---

## 8. Credenciales y configuración importante

> ⚠️ Esta sección es sensible. Guardala en un lugar seguro.

| Elemento | Valor |
|---|---|
| Email admin principal | vranjican22@gmail.com |
| Email admin secundario | info@mitodeportes.com.ar |
| Supabase URL | https://zvgpdvjosjtkqqidykdf.supabase.co |
| Supabase Anon Key | eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9... (ver en el código) |
| Google Client ID | 805829820098-6q89stfh62vlajqu367jj5r8mmubpl02.apps.googleusercontent.com |
| GitHub repo | https://github.com/vranjican22-droid/mitorating |

---

## 9. Tablas de la base de datos

La base de datos tiene estas tablas principales:

| Tabla | Para qué sirve |
|---|---|
| `jugadores` | Perfiles de todos los jugadores |
| `torneos` | Torneos publicados |
| `inscripciones` | Registros de quién se anotó a qué torneo |
| `partidos` | Partidos cargados con sus resultados |
| `historial_rating` | Registro de cada cambio de puntos |
| `clubes` | Clubes registrados |
| `productos` | Productos de la tienda Mito |
| `ordenes` | Compras en la tienda |
| `suscripciones_pro` | Suscripciones Mito Pro activas |
| `notificaciones` | Notificaciones enviadas a jugadores |

---

## 10. Flujos principales de la aplicación

### Flujo de un jugador nuevo

1. Entra a la URL
2. Hace clic en "Registrarse"
3. Completa nombre, email, contraseña, rol, provincia y ciudad
4. Se crea su cuenta en Supabase Auth + su perfil en `jugadores` con rating 0
5. Ingresa y ve su perfil con rating 0
6. Puede buscar torneos compatibles e inscribirse

### Flujo de carga de un partido

1. El jugador va a la sección "Partido"
2. Selecciona su pareja y los dos rivales
3. Ingresa el resultado (sets)
4. El sistema muestra el impacto estimado en el rating
5. Confirma → el partido queda como "pendiente"
6. Los otros 3 jugadores reciben notificación para confirmar
7. Cuando todos confirman (o el admin lo confirma manualmente), se actualiza el rating de los 4

### Flujo de publicación de un torneo (admin)

1. Admin va a Torneos → "+ Nuevo torneo"
2. Completa nombre, fecha, horario, dirección, rango de rating, cupos y precio
3. Publica → el torneo aparece para todos los jugadores compatibles
4. Los jugadores se inscriben desde la app
5. Admin puede ver inscriptos, generar cuadro y notificar participantes

---

## 11. Archivos críticos

| Archivo / Componente | Qué hace | Riesgo si se modifica mal |
|---|---|---|
| `mito-rating.html` | Es toda la aplicación | Puede romper el login, la app del jugador o el admin |
| Función `confirmar_partido()` en Supabase | Calcula y actualiza los ratings | Puede calcular mal los ratings de todos los jugadores |
| Función `calcular_elo()` en Supabase | Calcula el delta de puntos | Puede dar resultados incorrectos de rating |
| Variable `ADMIN_EMAILS` en el HTML | Define quiénes son admins | Un error puede bloquear el acceso al admin o dar acceso a quien no debe tenerlo |
| Variable `PROV_CIUDADES` en el HTML | Mapa de provincias y localidades | Si se borra, el selector de ciudades deja de funcionar |
| Tabla `historial_rating` en Supabase | Historial de todos los cambios de rating | Si se borra, se pierde el historial permanentemente |

---

## 12. Contradicciones o puntos a revisar

### Punto a revisar 1: Elementos de monetización fuera del alcance deportivo

**Descripción:** La aplicación tiene una tienda Mito con productos, un sistema de suscripción Pro y un cobro de fees por inscripciones. El prompt de documentación indica que la app "no está orientada a venta de productos, stock o facturación". Sin embargo, estos elementos son parte del modelo de negocio de Mito.

**Resolución:** La monetización es parte intencional del proyecto. No es una confusión. Se decidió integrarla desde el principio.

---

### Punto a revisar 2: Rating empieza en 0 vs. calibración inicial

**Descripción:** Todos los jugadores arrancan con rating 0. Cuando dos jugadores con rating 0 juegan entre sí, el algoritmo ELO no tiene información de quién es mejor y el resultado puede ser poco representativo.

**Riesgo:** Los primeros partidos de todos los jugadores son poco significativos para el rating.

**Pregunta para Seba:** ¿Querés implementar una encuesta de nivel inicial (tipo "¿Cuántos años jugás?") para asignar un rating estimado inicial en lugar de 0?

---

### Punto a revisar 3: Confirmación de partidos incompleta

**Descripción:** El sistema registra partidos y envía notificaciones para que los 4 jugadores confirmen, pero el flujo de confirmación desde la app del jugador no está implementado. Los jugadores reciben la notificación pero no tienen un botón para confirmar.

**Riesgo:** Los partidos quedan como "pendientes" indefinidamente a menos que el admin los confirme manualmente.

**Pregunta para Seba:** ¿Querés que implementemos el flujo de confirmación desde la app del jugador?

---

### Punto a revisar 4: Google login limitado a usuarios de prueba

**Descripción:** El login con Google solo funciona para emails que están en la lista de "usuarios de prueba" en Google Cloud. Para que cualquier persona pueda usar Google login, hay que "publicar la app" en Google Cloud.

**Riesgo:** Los jugadores nuevos que quieran usar Google login van a ver un error.

**Resolución recomendada:** Publicar la app en Google Cloud (proceso sencillo que no requiere pagar).

---

## 13. Riesgos del proyecto

| Riesgo | Probabilidad | Recomendación |
|---|---|---|
| Modificar el algoritmo ELO y romper el cálculo de ratings | Alta si no se documenta bien | Nunca tocar `confirmar_partido()` sin documentar el cambio primero |
| Subir un archivo HTML mal al GitHub y romper la app | Media | Siempre probar en incógnito antes de dar por válido un cambio |
| Borrar datos de jugadores o historial por error | Baja pero catastrófica | Nunca ejecutar `DELETE` masivo en Supabase sin un backup |
| Perder el contexto del proyecto en una nueva sesión de IA | Alta | Usar este documento al inicio de cada sesión |
| Cambios en varios lugares al mismo tiempo | Alta | Trabajar de a un cambio por vez, probar, luego seguir |
| Datos de rating inconsistentes si el cálculo falla a medias | Media | Los cambios de rating se hacen en una sola transacción SQL |

---

## 14. Forma recomendada de trabajar desde ahora

1. **Antes de empezar**, leer este documento y verificar que está actualizado.
2. **Definir UNA sola tarea** antes de abrir el chat con la IA.
3. **Explicar qué se quiere cambiar** y esperar confirmación de que la IA entendió bien.
4. **Indicar qué NO se quiere romper** en esa tarea.
5. **Hacer el cambio** de a una modificación.
6. **Probar** inmediatamente en una pestaña incógnita.
7. **Registrar el cambio** en este documento.
8. Recién entonces pasar a la siguiente tarea.

---

## 15. Checklist ANTES de cada cambio

Antes de pedirle a la IA que modifique algo, responder estas preguntas:

- [ ] ¿Qué quiero cambiar exactamente?
- [ ] ¿Qué parte ya funciona y no quiero romper?
- [ ] ¿Qué pantalla o módulo afecta este cambio?
- [ ] ¿Hay cálculo de rating involucrado?
- [ ] ¿Hay ranking o estadísticas que puedan verse afectadas?
- [ ] ¿Hay datos de jugadores reales que podrían verse afectados?
- [ ] ¿Cómo voy a probar que quedó bien?
- [ ] ¿Cómo voy a saber que no se rompió otra cosa?

---

## 16. Checklist DESPUÉS de cada cambio

Después de cualquier modificación, verificar:

- [ ] Abrí la app en una pestaña incógnita
- [ ] El login con email funciona
- [ ] Un jugador puede registrarse
- [ ] El admin puede entrar y ver el dashboard
- [ ] La lista de jugadores se carga en el admin
- [ ] El selector de provincia → ciudad funciona
- [ ] No hay errores en rojo en la consola del navegador (F12)
- [ ] Registré el cambio en este documento

---

## 17. Próxima tarea recomendada

**Tarea recomendada:** Implementar el flujo de confirmación de partidos desde la app del jugador.

**Motivo:** Hoy los partidos quedan como "pendientes" indefinidamente a menos que el admin los confirme manualmente. Para que el rating funcione automáticamente, los jugadores necesitan poder confirmar o rechazar un resultado desde su celular.

**Archivos involucrados:** `mito-rating.html` — sección de notificaciones y partidos del jugador.

**Riesgo:** Medio — puede afectar la lógica de confirmación y el cálculo de ratings.

**Cómo probarlo:** Cargar un partido de prueba y verificar que los otros 3 jugadores reciben notificación y pueden confirmar o rechazar desde la app.

**Por qué no conviene hacer otra cosa primero:** Sin este flujo, el sistema de rating no funciona automáticamente. El admin tiene que confirmar cada partido manualmente, lo cual no es escalable.

---

## 18. Preguntas pendientes para Seba

### Preguntas funcionales

- ¿Los torneos deberían tener categorías (1ra, 2da, 3ra, 4ta) además del rango de rating?
- ¿Un jugador puede pertenecer a más de un club?
- ¿Querés que los jugadores puedan editar su propio perfil (nombre, ciudad, rol)?
- ¿Cómo querés manejar los partidos de práctica vs. partidos de torneo? ¿Los dos suman al rating?
- ¿Querés que el sistema permita registrar partidos de 3 sets (con super tie-break)?

### Preguntas sobre puntuación

- ¿Querés implementar un sistema de 0 a 10 similar al UTR con decimales?
- ¿Los puntos deberían vencer después de 12 meses de inactividad?
- ¿Querés un ranking separado por categoría o nivel?
- ¿El campeón de un torneo debería sumar puntos extra además de los del rating ELO?
- ¿Querés considerar solo los últimos N partidos para el rating (tipo UTR: últimos 30)?

### Preguntas sobre datos

- ¿Qué pasa con un jugador que se registra con Google pero ya existía en la base con email manual?
- ¿Querés que los jugadores puedan subir una foto de perfil?
- ¿Querés que los jugadores puedan agregar su teléfono para que los otros los contacten?

### Preguntas sobre pantallas

- ¿Querés que haya una pantalla pública del ranking (sin necesidad de login)?
- ¿Querés que los clubes tengan su propio panel separado del panel de admin?
- ¿Querés una pantalla de perfil de otro jugador (para ver el rating e historial de alguien)?

### Preguntas técnicas necesarias

- ¿Querés conectar un procesador de pagos real (MercadoPago) para las inscripciones?
- ¿Querés que el Google login funcione para todos (no solo usuarios de prueba)?

---

## 19. Resumen ejecutivo para Seba

La aplicación **Mito Rating** está en producción y funciona. Tiene una URL pública, una base de datos real con jugadores reales, y un sistema de rating que ya calcula puntos correctamente.

Lo más importante que ya funciona:
- El login y registro de jugadores
- El panel de administración completo
- El sistema de torneos (crear, inscribir, generar cuadro)
- El algoritmo de rating ELO adaptado para pádel

Lo que más falta:
- El flujo de confirmación de partidos desde el celular del jugador
- El pago real de inscripciones (hoy es simulado)
- El Google login para todos los usuarios

Las partes más delicadas que no hay que tocar sin cuidado:
- El algoritmo que calcula el rating (función en Supabase)
- La lógica que decide quién es admin y quién es jugador

Antes de seguir desarrollando, se recomienda:
1. Confirmar con Seba cómo quiere que funcione el sistema de puntuación a largo plazo (¿ELO puro o algo más parecido al UTR?)
2. Implementar la confirmación de partidos desde el celular
3. Conectar un procesador de pagos real para las inscripciones

---

## 20. Cómo usar este documento en futuras sesiones

**Copiá y pegá este texto al inicio de cada nueva sesión con IA:**

---

Antes de hacer cualquier cambio, leé el archivo BASE_DE_CONOCIMIENTO_APP_PADEL.md que te voy a pegar a continuación.

Primero respondeme:
1. ¿Qué entendés del estado actual del proyecto?
2. ¿Qué funcionalidad vamos a tocar en esta sesión?
3. ¿Qué partes no debemos romper?
4. ¿Qué archivos pensás revisar?
5. ¿Qué archivos pensás modificar?
6. ¿Qué riesgo tiene el cambio?
7. ¿Qué prueba simple vamos a hacer al final?

No modifiques código hasta que yo confirme que entendiste bien.

---

*Documento generado el 13 de mayo de 2026. Actualizar cada vez que se haga un cambio importante al sistema.*
