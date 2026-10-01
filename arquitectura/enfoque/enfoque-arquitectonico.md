# Enfoque arquitectónico: Clean Architecture

| Elemento | Descripción aplicada al Marketplace |
|---|---|
| Patrón / enfoque arquitectónico | Clean Architecture (Arquitectura Limpia). |
| Objetivo | Separar responsabilidades y controlar las dependencias hacia el dominio. |
| ¿Qué problema resuelve? | Evita el acoplamiento entre la interfaz Angular, las reglas del negocio y las tecnologías externas, como bases de datos, API y servicios de pago. |
| Capas definidas | Presentación, Aplicación, Dominio e Infraestructura. |
| Beneficios | Facilita el mantenimiento y las pruebas unitarias. Permite cambiar implementaciones técnicas sin modificar innecesariamente las reglas del negocio. Mejora la organización y separación de responsabilidades del código. |
| Driver que responde | DA06 - Mantenibilidad (y DA04 - Integración con pagos). |

## Regla de dependencia
Las dependencias del código **siempre apuntan hacia el dominio**:

1. El dominio no importa nada de las capas externas (ni Angular, ni HttpClient, ni RxJS).
2. Los casos de uso solo conocen entidades y contratos (interfaces).
3. Los adaptadores de infraestructura implementan los contratos; son intercambiables.
4. Cambiar de tecnología significa cambiar `app.config.ts`, no el dominio.

## Diagrama del enfoque

```mermaid
flowchart LR
    Usuario["Usuario - Cliente"]

    subgraph PRESENTACION["PRESENTACIÓN - src/app/presentacion"]
        CatComp["CatalogoComponent<br/>lista y filtro de productos"]
        Estado["EstadoCarrito<br/>servicio de estado"]
        CarComp["CarritoComponent<br/>resumen y confirmar compra"]
        AppComp["AppComponent<br/>shell de la aplicación"]
    end

    subgraph APLICACION["APLICACIÓN - casos de uso"]
        UC1["ConsultarCatalogoCasoUso"]
        UC2["AgregarAlCarritoCasoUso"]
        UC3["RegistrarCompraCasoUso"]
    end

    subgraph DOMINIO["DOMINIO - núcleo sin dependencias externas"]
        subgraph MODELOS["Modelos - entidades y reglas"]
            Producto["Producto"]
            Carrito["Carrito"]
            Pedido["Pedido"]
            Precios["precios.ts<br/>reglas de precio"]
        end
        subgraph CONTRATOS["Contratos - puertos"]
            IRepoProd["«interface» RepositorioProductos"]
            IRepoPed["«interface» RepositorioPedidos"]
            IPagos["«interface» ProcesadorPagos"]
            INotif["«interface» NotificadorCliente"]
        end
    end

    subgraph INFRA["INFRAESTRUCTURA - adaptadores"]
        RepoProd["RepositorioProductosMemoria<br/>RepositorioProductosHttp"]
        RepoPed["RepositorioPedidosMemoria"]
        Pagos["ProcesadorPagosSimulado"]
        Notif["NotificadorConsola<br/>NotificadorWhatsApp"]
    end

    Config["app.config.ts<br/>raíz de composición: elige qué adaptador cumple cada contrato"]
    API["Marketplace API REST<br/>sistema externo"]

    Usuario --> CatComp
    Usuario --> CarComp
    CatComp --> UC1
    CarComp --> UC2
    CarComp --> UC3
    CatComp --> Estado
    CarComp --> Estado

    UC1 -.-> IRepoProd
    UC2 -.-> Carrito
    UC3 -.-> Pedido
    UC3 -.-> IRepoPed
    UC3 -.-> IPagos
    UC3 -.-> INotif

    RepoProd -.->|"implementa"| IRepoProd
    RepoPed -.->|"implementa"| IRepoPed
    Pagos -.->|"implementa"| IPagos
    Notif -.->|"implementa"| INotif

    RepoProd -->|"HTTP / JSON"| API
    Config -.->|"registra"| INFRA
```

**Leyenda:**
- Flecha continua: llamada en tiempo de ejecución.
- Flecha punteada: dependencia de código (`import`), siempre hacia el centro.
- "implementa": el adaptador cumple el contrato definido en el dominio (inversión de dependencias).

## Diferencia con el estilo arquitectónico

| | Estilo (estilo-arquitectonico.md) | Enfoque (este archivo) |
|---|---|---|
| Responde | ¿Cómo es el sistema globalmente? | ¿Cómo se organiza por dentro? |
| Decisión | Monolito modular en capas | Clean Architecture |
| Dependencias | De arriba hacia abajo | Hacia el dominio (invertidas) |