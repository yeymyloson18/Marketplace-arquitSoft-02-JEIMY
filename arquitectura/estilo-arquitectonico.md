# Estilo arquitectónico

## Estilo seleccionado
**Monolito modular organizado en capas.**

- **Monolito:** todo el backend es una sola aplicación (Node.js + Express), que se ejecuta en un único proceso y tiene un solo despliegue.
- **Modular:** por dentro se divide en módulos independientes: Usuarios, Sellers, Catálogo, Carrito y Pedidos.
- **En capas:** cada módulo se organiza en tres capas: Presentación, Lógica de negocio y Datos.

El monolito es la **unidad de despliegue** y las capas son la **organización lógica**. Ambos coexisten.

## Justificación

| Driver | Cómo lo responde el estilo |
|---|---|
| DA01 - Escalabilidad | La aplicación puede replicarse horizontalmente detrás de un balanceador. |
| DA05 - API REST | El cliente web se comunica con el backend mediante una API REST (HTTPS/JSON). |
| DA06 - Mantenibilidad | Los módulos separan responsabilidades; un cambio en un módulo no afecta a los demás. |

**Alternativa descartada:** microservicios. Para el tamaño actual del marketplace añaden complejidad de despliegue, comunicación y datos sin un beneficio claro.

## Diagrama del estilo arquitectónico

```mermaid
flowchart TD
    subgraph ACTORES["ACTORES"]
        Cliente["Cliente"]
        Seller["Seller"]
        Admin["Administrador"]
    end

    Web["Cliente Web<br/>Navegador - HTML / CSS / JavaScript"]

    subgraph MONOLITO["MONOLITO - Marketplace Backend - Node.js + Express - un solo despliegue"]
        MW["Middlewares transversales<br/>cors - express.json - auth JWT - validación - manejo de errores - logger"]

        subgraph PRES["1. CAPA DE PRESENTACIÓN - rutas y controladores"]
            RU["usuarios.routes + controller"]
            RS["sellers.routes + controller"]
            RC["catalogo.routes + controller"]
            RCa["carrito.routes + controller"]
            RP["pedidos.routes + controller"]
        end

        subgraph NEG["2. CAPA DE LÓGICA DE NEGOCIO - servicios"]
            SU["usuarios.service<br/>registro, login, roles"]
            SS["sellers.service<br/>alta de tiendas, validación"]
            SC["catalogo.service<br/>productos, categorías, stock"]
            SCa["carrito.service<br/>ítems, totales"]
            SP["pedidos.service<br/>checkout, estados, pago y envío"]
        end

        subgraph DAT["3. CAPA DE DATOS - repositorios"]
            DU["usuarios.repository"]
            DS["sellers.repository"]
            DC["catalogo.repository"]
            DCa["carrito.repository"]
            DP["pedidos.repository"]
            ORM["Acceso a datos compartido<br/>ORM - modelos - pool de conexiones"]
        end
    end

    BD[("PostgreSQL<br/>marketplace_db")]
    Pago["Pasarela de pagos<br/>sistema externo"]
    Envio["Servicio de envíos<br/>sistema externo"]

    Cliente --> Web
    Seller --> Web
    Admin --> Web
    Web -->|"HTTPS - JSON /api/v1"| MW
    MW --> PRES

    RU --> SU
    RS --> SS
    RC --> SC
    RCa --> SCa
    RP --> SP

    SU --> DU
    SS --> DS
    SC --> DC
    SCa --> DCa
    SP --> DP

    SCa -.->|"usa"| SC
    SP -.->|"usa"| SCa

    DU --> ORM
    DS --> ORM
    DC --> ORM
    DCa --> ORM
    DP --> ORM
    ORM -->|"SQL - TCP 5432"| BD

    SP -->|"HTTPS / REST"| Pago
    SP -->|"HTTPS / REST"| Envio
```

## Reglas de la arquitectura

1. Cada capa solo invoca a la capa inmediatamente inferior.
2. Un módulo no accede al repositorio ni a las tablas de otro módulo.
3. La comunicación entre módulos se hace llamando a su servicio (flechas punteadas).
4. Todo se ejecuta en un único proceso Node.js con una única base de datos.