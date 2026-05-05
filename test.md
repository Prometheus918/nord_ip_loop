flowchart TB
    subgraph Browser["🖥️ Browser (Client)"]
        UI["React 18 + Vite + Tailwind<br/>shadcn UI primitives"]
        Store["Zustand auth store"]
        Pages["Pages: Dashboard · Agents · NewAgent ·<br/>AgentDetail · Reports · ReportDetail ·<br/>SharedReport · Settings · Login · Signup"]
        Axios["Axios client<br/>(JWT in localStorage,<br/>3s status polling)"]
        PDF["jsPDF report export"]

        Pages --> Store
        Pages --> Axios
        Pages --> PDF
    end

    subgraph Server["⚙️ Express Server (Node + TypeScript)"]
        direction TB
        Routes["/api routes"]
        AuthMW["JWT auth middleware<br/>+ role guard"]
        ErrMW["Error handler<br/>(Zod validation)"]

        subgraph Controllers
            AuthCtrl["authController"]
            AgentCtrl["agentController"]
            RunCtrl["testRunController"]
            RepCtrl["reportController"]
            DashCtrl["dashboardController"]
            SetCtrl["settingsController"]
        end

        subgraph Services
            Crypto["crypto.ts<br/>(AES-256-GCM)"]
            JWTLib["jwt.ts"]
            Connector["agentConnector<br/>(calls external agents,<br/>decrypts key,<br/>extracts response by path)"]
            Runner["testRunner<br/>(incremental progress,<br/>auto-triggers report)"]
        end

        subgraph ClaudePipelines["🤖 Claude Pipelines"]
            P1["1️⃣ Understanding<br/>(security profile)"]
            P2["2️⃣ Test Generation<br/>(≥30 tailored cases)"]
            P2b["2️⃣b Evaluation<br/>(per-result scoring)"]
            P3["3️⃣ Reporting<br/>(executive + technical audit)"]
        end

        Queue["Bull Queue<br/>(test-runs, concurrency=2)"]

        Routes --> AuthMW --> Controllers
        Controllers --> ErrMW
        AgentCtrl --> Crypto
        AgentCtrl --> P1
        AgentCtrl --> P2
        AgentCtrl --> Queue
        Queue --> Runner
        Runner --> Connector
        Runner --> P2b
        Runner --> P3
        AuthCtrl --> JWTLib
    end

    subgraph DataLayer["💾 Data Layer"]
        Postgres[("PostgreSQL<br/>via Prisma ORM")]
        Redis[("Redis<br/>(Bull jobs)")]
    end

    subgraph External["🌐 External"]
        Anthropic["Anthropic API<br/>(Claude Mythos)"]
        CustomerAgent["Customer's AI Agent<br/>(any HTTP/JSON endpoint)"]
        MockAgent["Mock Agent (:4000)<br/>local dev only"]
    end

    Browser <-->|HTTPS + JWT| Routes
    Browser -.->|public<br/>/share/:token| RepCtrl

    Controllers <--> Postgres
    Runner <--> Postgres
    Queue <--> Redis

    P1 --> Anthropic
    P2 --> Anthropic
    P2b --> Anthropic
    P3 --> Anthropic

    Connector -->|attack prompt| CustomerAgent
    Connector -->|attack prompt| MockAgent

    classDef browser fill:#e0f2fe,stroke:#0369a1
    classDef server fill:#ede9fe,stroke:#6d28d9
    classDef data fill:#fef3c7,stroke:#b45309
    classDef ext fill:#fee2e2,stroke:#b91c1c
    class Browser browser
    class Server server
    class DataLayer data
    class External ext
