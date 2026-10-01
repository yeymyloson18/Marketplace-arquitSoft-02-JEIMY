# Drivers arquitectónicos

Requisitos, atributos de calidad y restricciones que influyen de manera significativa
en las decisiones de arquitectura del sistema.

| ID | Driver arquitectónico | Origen | ¿Por qué influye en la arquitectura? |
|---|---|---|---|
| DA01 | El sistema debe soportar un incremento importante de usuarios durante campañas comerciales. | AC03 – Escalabilidad | Puede influir en la estrategia de escalamiento y despliegue. |
| DA02 | El sistema debe mantener tiempos de respuesta adecuados durante una alta concurrencia. | AC01 – Rendimiento | Puede influir en la comunicación entre componentes, procesamiento y almacenamiento. |
| DA03 | El sistema debe proteger los datos de usuarios y operaciones de compra. | AC04 – Seguridad | Puede influir en autenticación, autorización y protección de datos. |
| DA04 | El sistema debe integrarse con una pasarela de pago externa mediante una API. | RC04 – Pasarela de pago | Condiciona la forma de comunicación e integración con servicios externos. |
| DA05 | El sistema debe utilizar una API REST para la comunicación entre frontend y backend. | RC03 – API REST | Limita las alternativas de comunicación entre las partes del sistema. |
| DA06 | El sistema debe permitir modificar funcionalidades sin afectar innecesariamente otros módulos. | AC05 – Mantenibilidad | Influye en la separación de responsabilidades, modularidad y dependencias internas. |

## Relación entre drivers y decisiones

| Driver | Problema que plantea | Decisión que responde |
|---|---|---|
| DA01 - Escalabilidad | Aumentarán usuarios en campañas | Monolito modular con posibilidad de escalamiento horizontal |
| DA02 - Rendimiento | Habrá alta concurrencia | Incorporar caché y optimizar comunicación/procesamiento |
| DA03 - Seguridad | Hay datos sensibles | Autenticación y autorización |
| DA04 - Pago externo | Hay que comunicarse con una pasarela | Integración mediante API y adaptadores |
| DA05 - API REST | Frontend/backend deben comunicarse mediante REST | Separar interfaz y backend mediante API REST |
| DA06 - Mantenibilidad | Cambios no deben afectar otros módulos | Modularidad + Clean Architecture |