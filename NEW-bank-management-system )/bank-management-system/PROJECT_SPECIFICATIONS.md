# MaBank - Project Specifications

## Project Goal

**MaBank** is a Java Swing banking management system designed for administrative operations. The application enables bank staff to:

- **Manage Clients** - Create, update, delete, and search client profiles
- **Manage Employees** - Administer employees, managers, and directors with role-based access
- **Manage Bank Accounts** - Create and manage different account types
- **Process Transactions** - Execute deposits, withdrawals, and transfers
- **Generate Reports** - View financial analytics and statistics
- **Audit Trail** - Maintain complete history of employee actions

## Technical Requirements

### Technology Stack
- **Language**: Java (JDK 25+)
- **UI Framework**: Swing (native Java GUI)
- **Data Storage**: In-memory arrays (no database)
- **Architecture**: Object-oriented design with interfaces and abstract classes

### Storage Architecture

All application data is stored exclusively in **Java arrays in memory**, managed by the `BanqueManager` singleton:

```java
private Client[] clients;              // All bank clients
private Employee[] employes;           // All employees (Employe, Manager, Directeur)
private Compte[] comptes;              // All bank accounts
private Transaction[] transactions;    // All transactions (Depot, Retrait, Virement)
```

**Key Constraint**: Dynamic arrays grow using `Arrays.copyOf(...)` when capacity is reached.

### Data Persistence

⚠️ **Important**: The application does **NOT** use:
- Relational databases (SQL)
- File-based persistence (`data/` folder, `.dat` files)
- Serialization frameworks
- `PersistanceManager` class

**Consequence**: All data created during a session is **lost on application exit**.

## Component Architecture

### Core Components

```
┌─────────────────────────────────────────────┐
│           Application.java                  │  Main entry point
├─────────────────────────────────────────────┤
│  ┌──────────────┐  ┌────────────────────┐  │
│  │  UI Layer    │  │ BanqueManager      │  │
│  │ (Swing)      │──│ (Central Manager)  │  │
│  └──────────────┘  └────────────────────┘  │
│       ↓                    ↓                │
│  ┌──────────────────────────────────────┐  │
│  │  Business Logic (Models)             │  │
│  │ User, Client, Employee, Compte, etc. │  │
│  └──────────────────────────────────────┘  │
│       ↓                                     │
│  ┌──────────────────────────────────────┐  │
│  │  Interfaces & Exception Hierarchy    │  │
│  └──────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
```

### Package Organization

- **`Application.java`** - Application launcher and UI coordinator
- **`gestionnaires/`** - Central management class (`BanqueManager`)
- **`modeles/`** - Business domain classes (entities and value objects)
- **`interfaces/`** - Contracts defining behavioral capabilities
- **`exceptions/`** - Custom exception hierarchy for error handling
- **`ui/`** - Swing-based GUI windows and dialogs

## Class Hierarchy

### User Hierarchy
```
User (abstract)
├─ id, nom, email, dateCreation
├─ Client
│  └─ CompteClient[] (accounts)
└─ Employee (implements Auditable)
   ├─ role, salaire, dateEmbauche
   ├─ historique (action audit log)
   ├─ Employe (basic employee)
   ├─ Manager (team supervisor)
   └─ Directeur (regional director)
```

### Account (Compte) Hierarchy
```
Compte (abstract)
├─ numero, solde, proprietaire, dateCreation
├─ calculerInteret()
├─ appliquerFrais()
├─ CompteInvestissement (investment)
├─ CompteClient (client account)
└─ CompteEmployee (employee account)
```

### Transaction Hierarchy
```
Transaction (abstract)
├─ id, date, montant, statut
├─ Depot (deposit)
├─ Retrait (withdrawal)
└─ Virement (transfer)
```

## Object-Oriented Design Patterns

### Core OOP Concepts Implemented

1. **Abstraction**
   - Abstract base classes: `User`, `Compte`, `Transaction`
   - Define common structure and behavior
   - Force subclasses to implement domain-specific logic

2. **Inheritance**
   - `Employee` extends `User`
   - `Employe`, `Manager`, `Directeur` extend `Employee`
   - Account types extend `Compte`
   - Transaction types extend `Transaction`

3. **Polymorphism**
   - Dynamic method dispatch based on runtime type
   - Example: `compte.calculerInteret()` behaves differently for `CompteInvestissement` vs `CompteClient`
   - Supports extensibility without modifying existing code

4. **Encapsulation**
   - Private fields with controlled public access
   - Getter/setter methods validate data
   - Business logic protected from invalid states

5. **Interface-Based Design**
   - `Auditable` - Classes that record user actions
   - `Assurable` - Classes that can be insured
   - `Authentifiable` - User authentication contract
   - `Notifiable` - Event notification capability
   - `Placable` - Investment placement capability

6. **Exception Handling**
   - Custom exception hierarchy for domain errors
   - Specific exceptions for business constraints
   - Propagates through calling code for centralized handling

7. **Collections & Arrays**
   - Dynamic array resizing using `Arrays.copyOf()`
   - Linear search for O(n) lookups
   - Simple but transparent data management

## Data Flow

### Typical Transaction Flow

```
User Input (UI) 
    ↓
Validation (BanqueManager)
    ↓
Business Logic (Model class)
    ↓
State Update (Array modification)
    ↓
Audit Logging (Auditable interface)
    ↓
UI Refresh
```

### Example: Withdraw Money

1. User enters amount in `FenetreTransactions`
2. `BanqueManager.effectuerRetrait(compte, montant)` called
3. `Retrait` transaction created
4. `compte.retirer(montant)` executes withdrawal logic
5. `SoldeInsufficientException` thrown if balance insufficient
6. If successful: solde updated, transaction recorded
7. `employee.enregistrerAction("Retrait effectué...")` logs audit
8. UI updates to show new balance

## Error Handling Strategy

### Exception Hierarchy

```
Exception
└─ BanqueException (base for all banking errors)
   ├─ AuthenticationException
   ├─ ClientExisteException
   ├─ CompteInexistantException
   ├─ DateInvalideException
   ├─ MontantInvalidException
   ├─ OperationNonAutoriseeException
   └─ SoldeInsufficientException
```

### Error Scenarios

- **SoldeInsufficientException**: Withdrawal exceeds available balance
- **CompteInexistantException**: Referenced account does not exist
- **ClientExisteException**: Attempting to register existing client
- **MontantInvalidException**: Invalid transaction amount (negative, zero)
- **DateInvalideException**: Invalid date for transaction
- **OperationNonAutoriseeException**: User lacks permission for operation

## Functional Requirements

### Client Management
- Create new client with validation
- Assign multiple bank accounts to client
- Update client information
- Delete client (with cascade to accounts)
- Search/list all clients

### Employee Management
- Hire employees with role assignment
- Salary and bonus management
- Team management for managers
- Regional assignment for directors
- Audit history for compliance

### Account Management
- Create accounts of different types
- Assign account to client or employee
- Calculate interest (savings accounts)
- Apply fees (checking accounts)
- View account balance and transactions

### Transaction Processing
- Deposits (add to account)
- Withdrawals (remove from account)
- Transfers (between accounts)
- Transaction history
- Balance verification

### Reporting & Analytics
- Total assets by client
- Employee productivity metrics
- Transaction volume by type
- Financial statistics

## Compilation and Execution

### Build
```bash
mkdir -p out
find src -name "*.java" | xargs javac -d out
```

### Run
```bash
java -cp out Application
```

### Result
Launches Swing GUI with main window showing options to:
- Manage clients
- Manage employees
- Manage accounts
- Process transactions
- View reports

## Design Constraints

✓ Pure Java (no external libraries except Swing)  
✓ In-memory storage only (no database)  
✓ Single-threaded (no concurrency)  
✓ Swing GUI (not web-based)  
✓ Custom exception hierarchy (no generic exceptions)  
✓ No file persistence (clean slate on restart)  

## Success Criteria

- ✅ Application compiles without errors
- ✅ GUI launches and all windows functional
- ✅ Transactions execute with proper validation
- ✅ Audit trail records all employee actions
- ✅ Exception handling prevents invalid states
- ✅ Code demonstrates OOP principles
- ✅ Clean separation of concerns (UI/Business/Data)

## OOP Concepts

- Abstraction: `User`, `Compte`, `Transaction`
- Inheritance: clients/employees, account types, transaction types
- Polymorphism: account and transaction behavior by concrete type
- Encapsulation: private fields and public methods
- Method overloading: `ajouterClient(...)`
- Custom exceptions: banking operation errors
- Arrays: central in-memory data storage
