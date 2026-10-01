# Decisiones arquitectónicas (ADR)

Un **ADR (Architecture Decision Record)** o Registro de Decisión Arquitectónica documenta
las decisiones importantes tomadas durante el diseño de la arquitectura, junto con su justificación.

## Resumen de decisiones

| ID | Decisión arquitectónica | Driver relacionado | Justificación | Resultado |
|---|---|---|---|---|
| ADR-001 | Monolito modular | DA01 - Escalabilidad; DA06 - Mantenibilidad | Organizar las funcionalidades en módulos independientes dentro de una misma aplicación desplegable. | Módulos de Catálogo, Carrito, Pedidos, Pagos y Usuarios. |
| ADR-002 | Clean Architecture | DA06 - Mantenibilidad | Separar las reglas del negocio de los detalles tecnológicos. | Dominio, Aplicación, Infraestructura y Presentación. |
| ADR-003 | Estrategia de caché | DA02 - Rendimiento | Reducir consultas repetitivas a la fuente de datos. | Caché para información de consulta frecuente. |
| ADR-004 | Integración de pagos mediante interfaces y adaptadores | DA04 - Integración con pagos | Desacoplar los casos de uso del proveedor de pagos. | Contrato de pagos y adaptador para la pasarela externa. |

---

## ADR-001: Monolito modular

- **Contexto:** el marketplace debe soportar más usuarios en campañas (DA01) y permitir cambios sin afectar otros módulos (DA06).
- **Alternativas evaluadas:** monolito tradicional sin módulos; microservicios.
- **Decisión:** construir una sola aplicación desplegable, organizada en módulos independientes: Usuarios, Catálogo, Carrito, Pedidos y Pagos.
- **Consecuencias:**
  - ✅ Más simple de desarrollar, probar y desplegar que los microservicios.
  - ✅ Puede escalar horizontalmente con varias instancias.
  - ✅ Los módulos podrían separarse como servicios en el futuro.
  - ⚠️ Se escala la aplicación completa, no cada módulo por separado.

## ADR-002: Clean Architecture

- **Contexto:** las reglas del negocio no deben depender de la interfaz, la base de datos ni los servicios externos (DA06).
- **Alternativas evaluadas:** arquitectura en capas tradicional; MVC.
- **Decisión:** organizar cada módulo en cuatro capas (Dominio, Aplicación, Infraestructura y Presentación), con las dependencias apuntando siempre hacia el dominio.
- **Consecuencias:**
  - ✅ El dominio no depende de frameworks ni de la base de datos.
  - ✅ Facilita las pruebas unitarias de las reglas del negocio.
  - ✅ Permite cambiar tecnologías sin modificar el negocio.
  - ⚠️ Requiere más archivos e interfaces que una estructura simple.

## ADR-003: Estrategia de caché

- **Contexto:** en campañas habrá alta concurrencia y consultas repetidas del catálogo (DA02).
- **Alternativas evaluadas:** consultar siempre la base de datos; escalar solo la base de datos.
- **Decisión:** guardar en caché la información de consulta frecuente, como el catálogo de productos.
- **Consecuencias:**
  - ✅ Respuestas más rápidas y menos carga en la base de datos.
  - ⚠️ Hay que actualizar la caché cuando cambian los datos.

## ADR-004: Integración de pagos mediante interfaces y adaptadores

- **Contexto:** el sistema debe usar una pasarela de pago externa (DA04) que podría cambiar en el futuro.
- **Alternativas evaluadas:** llamar a la pasarela directamente desde los casos de uso.
- **Decisión:** definir un contrato de pagos (interface) en el núcleo y un adaptador que lo implemente para la pasarela elegida.
- **Consecuencias:**
  - ✅ Los casos de uso no dependen del proveedor de pagos.
  - ✅ Cambiar de pasarela solo requiere un adaptador nuevo.
  - ✅ Permite usar un adaptador simulado para pruebas.
  - ⚠️ Agrega una capa de abstracción adicional.