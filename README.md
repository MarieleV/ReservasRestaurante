<h1 align="center">Sistema de Reserva de Restaurante</h1>

<p align="center">
  Plataforma web para gestão de reservas de mesas, com área do cliente e painel administrativo.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Arquitetura-Monolito%20Modular-334155?style=for-the-badge" alt="Arquitetura: monolito modular" />
  <img src="https://img.shields.io/badge/Status-Em%20Desenvolvimento-475569?style=for-the-badge" alt="Status: em desenvolvimento" />
  <img src="https://img.shields.io/badge/Auth-JWT-64748B?style=for-the-badge&logo=jsonwebtokens&logoColor=white" alt="Autenticação JWT" />
</p>

---

<p align="center">
Sistema que permite aos clientes do restaurante reservar mesas online e à administração gerenciar essas reservas, controlando horários e disponibilidade.
</p>

<p align="center">
  <a href="#-funcionalidades-principais">Funcionalidades</a> ·
  <a href="#-requisitos-funcionais-rf">Requisitos Funcionais</a> ·
  <a href="#-requisitos-não-funcionais-rnf">Requisitos Não Funcionais</a> ·
  <a href="#-arquitetura">Arquitetura</a> ·
  <a href="#-tech-stack">Tech Stack</a>
</p>

---

##  → Funcionalidades Principais

<table align="center" width="100%">
  <tr>
    <td width="33%" valign="top">
      <h3>CRUD de Clientes</h3>
      <p>Cadastro de clientes (nome, e-mail, telefone, senha), edição e exclusão de cadastro e listagem dos clientes cadastrados.</p>
    </td>
    <td width="33%" valign="top">
      <h3>CRUD de Reservas</h3>
      <p>Criação de reservas (data, horário, número de pessoas), edição, exclusão e listagem das reservas ativas.</p>
    </td>
    <td width="33%" valign="top">
      <h3>Transação</h3>
      <p>Cliente e restaurante podem confirmar uma reserva, garantindo a mesa, ou cancelá-la, liberando a mesa para novas reservas.</p>
    </td>
  </tr>
</table>

<br/>

##  → Requisitos Funcionais (RF)

| ID | Descrição |
| :---: | :--- |
| **RF001** | O sistema deve permitir o cadastro de novos clientes com nome, e-mail, telefone e CPF. |
| **RF002** | O sistema deve permitir que os clientes editem e excluam seus cadastros. |
| **RF003** | O sistema deve permitir que administradores visualizem a lista de clientes cadastrados. |
| **RF004** | O sistema deve permitir que os clientes façam reservas informando data, horário e número de pessoas. |
| **RF005** | O sistema deve permitir a edição ou o cancelamento de reservas. |
| **RF006** | O sistema deve exibir aos administradores a lista de reservas ativas. |
| **RF007** | O sistema deve permitir a confirmação de reservas. |
| **RF008** | O sistema deve permitir que o cliente acompanhe o status da sua reserva (pendente, confirmada, cancelada). |

<br/>

##  → Requisitos Não Funcionais (RNF)

| ID | Descrição |
| :---: | :--- |
| **RNF001** | O sistema deve usar um banco de dados para armazenar clientes, reservas e disponibilidade de mesas. |
| **RNF002** | O tempo de resposta do sistema deve ser inferior a 10 segundos. |
| **RNF003** | O sistema deve suportar no mínimo 30 reservas simultâneas sem perda de desempenho. |
| **RNF004** | O sistema deve ter autenticação segura para clientes e administração, com e-mail e senha. |
| **RNF005** | O sistema deve aceitar reservas apenas no horário de funcionamento do restaurante. |
| **RNF006** | As senhas dos usuários devem ser armazenadas de forma segura. |
| **RNF007** | O sistema deve apresentar mensagens de erro claras e compreensíveis. |
| **RNF008** | O sistema deve estar disponível a maior parte do tempo. |
| **RNF009** | O sistema deve limitar as reservas simultâneas à capacidade total de mesas do restaurante. |
| **RNF010** | O sistema deve impedir reservas duplicadas para o mesmo cliente. |

<br/>

##  → Arquitetura

<p>
  <img src="https://img.shields.io/badge/Monolito%20Modular-334155?style=flat-square" alt="Monolito modular" />
  <img src="https://img.shields.io/badge/Feature--Sliced-334155?style=flat-square" alt="Feature-Sliced" />
  <img src="https://img.shields.io/badge/Separa%C3%A7%C3%A3o%20por%20dom%C3%ADnio-334155?style=flat-square" alt="Separação por domínio" />
</p>

O sistema é um **monolito modular**: uma única aplicação, mas com **separação lógica clara por domínio de negócio** (autenticação, cliente, reserva e administração).

Cada domínio fica em sua própria pasta, como um "futuro microsserviço", e o que é genérico fica em uma área compartilhada. Essas fronteiras mantêm o código organizado hoje e facilitam uma eventual migração para microsserviços: se a "Reserva" precisar virar um serviço isolado, sua lógica já estará encapsulada.

<details>
  <summary><b>Estrutura do front-end</b></summary>
  <br/>

```text
front-end/src/
├── shared/                 # Genérico e reaproveitável entre domínios
│   ├── components/         # Button, Label, Input, StatusBadge, NavBar, Tabs
│   ├── utils/              # formatDate, todayStr, load (localStorage)
│   └── types/              # (Opcional) Tipos globais que cruzam domínios
│
├── modules/                # Separação lógica por domínio
│   ├── auth/               # Autenticação
│   │   ├── pages/          # LandingPage.tsx, RegisterPage.tsx
│   │   └── hooks/          # useAuth.ts (lógica de login/registro)
│   │
│   ├── client/             # Cliente
│   │   ├── components/     # Formulário de edição de perfil
│   │   └── pages/          # ClientDashboard.tsx
│   │
│   ├── reservation/        # Reservas
│   │   ├── components/     # ReservationCard.tsx
│   │   ├── pages/          # ReservationFormPage.tsx
│   │   └── types/          # Reservation, ReservationStatus
│   │
│   └── admin/              # Administração
│       └── pages/          # AdminDashboard.tsx
│
├── routes/                 # Orquestração das telas
│   └── AppRoutes.tsx       # Configuração do React Router DOM
│
├── App.tsx                 # Ponto de entrada (apenas provedores)
├── main.tsx
└── index.css
```

</details>

<details>
  <summary><b>Estrutura do back-end</b></summary>
  <br/>

```text
back-end/
├── src/
│   ├── config/                  # Banco de dados e variáveis de ambiente
│   ├── middlewares/             # Middlewares globais
│   │   ├── authMiddleware.js
│   │   └── errorMiddleware.js
│   │
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── authController.js
│   │   │   └── authRoutes.js
│   │   │
│   │   ├── clientes/
│   │   │   ├── clienteController.js
│   │   │   ├── clienteModel.js
│   │   │   └── clienteRoutes.js
│   │   │
│   │   ├── reservas/
│   │   │   ├── reservaController.js
│   │   │   ├── reservaModel.js
│   │   │   └── reservaRoutes.js
│   │   │
│   │   └── usuarios/            # Administração
│   │       ├── usuarioController.js
│   │       ├── usuarioModel.js
│   │       └── usuarioRoutes.js
│   │
│   └── app.js                   # Inicialização e registro das rotas
│
├── .github/workflows/
│   └── sonarcloud.yml           # Análise de qualidade de código
├── package.json
└── package-lock.json
```

</details>

<br/>

##  → Tech Stack

<div align="center">
  <table align="center" cellspacing="10" cellpadding="10">
    <tr>
      <td align="center" width="16%">
        <img src="https://cdn.simpleicons.org/javascript/CBD5E1" width="40" height="40" alt="JavaScript"/><br/>JavaScript
      </td>
      <td align="center" width="16%">
        <img src="https://cdn.simpleicons.org/nodedotjs/CBD5E1" width="40" height="40" alt="Node.js"/><br/>Node.js
      </td>
      <td align="center" width="16%">
        <img src="https://cdn.simpleicons.org/express/CBD5E1" width="40" height="40" alt="Express"/><br/>Express
      </td>
      <td align="center" width="16%">
        <img src="https://cdn.simpleicons.org/mysql/CBD5E1" width="40" height="40" alt="MySQL"/><br/>MySQL
      </td>
      <td align="center" width="16%">
        <img src="https://cdn.simpleicons.org/jsonwebtokens/CBD5E1" width="40" height="40" alt="JWT"/><br/>JWT
      </td>
      <td align="center" width="16%">
        <img src="https://cdn.simpleicons.org/githubactions/CBD5E1" width="40" height="40" alt="GitHub Actions"/><br/>GitHub Actions
      </td>
    </tr>
  </table>
</div>

<details>
  <summary><b>Por que essas tecnologias?</b></summary>
  <br/>

| Camada | Tecnologia | Justificativa |
| :---: | :---: | :--- |
| **Front-end** | JavaScript | Base da interface usada por clientes e administradores. |
| **Back-end** | Node.js + Express | O Node é leve e eficiente para aplicações com muitas requisições simultâneas, como um sistema de reservas. O Express organiza rotas e lógica de negócio de forma clara. |
| **Banco de dados** | MySQL | Ampla documentação, bom suporte de ferramentas e estabilidade reconhecida. |
| **Autenticação** | JWT | Autenticação sem sessões no servidor, o que contribui para a escalabilidade. |

</details>

<br/>

<p align="center">
  <a href="https://github.com/MarieleV">
    <img src="https://img.shields.io/badge/Mariele%20Vieira-334155?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" />
  </a>
  <a href="https://www.linkedin.com/in/mariele-vieira" target="_blank">
    <img src="https://img.shields.io/badge/LinkedIn-475569?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn" />
  </a>
  <a href="mailto:silvamariele797@gmail.com">
    <img src="https://img.shields.io/badge/E--mail-64748B?style=for-the-badge&logo=gmail&logoColor=white" alt="E-mail" />
  </a>
</p>
