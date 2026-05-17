Make these code changes?
PROJECT_SPECIFICATIONS.md

md
# 📋 Projet Gestion de Banque - Spécifications Complètes

## 🎯 Objectif General
Concevoir une application complète de gestion bancaire en Java qui démonstrate une maîtrise complète des concepts orientés objet (POO) et des bonnes pratiques de programmation.

---

## 1️⃣ Architecture et Structure

### 1.1 Abstraction
**Objectif** : Empêcher la création directe d'objets génériques

- **Classe abstraite `Compte`**
  - Attributs : `numeroCompte`, `solde`, `titulaire`, `dateCreation`
  - Méthodes abstraites : `calculerInteret()`, `appliquerFrais()`, `afficherDetails()`
  - ❌ Ne pas pouvoir instancier directement `new Compte()`

- **Classe abstraite `Personnel`**
  - Attributs : `id`, `nom`, `email`, `salaire`
  - Méthodes abstraites : `calculerBonus()`, `afficherRole()`

- **Classe abstraite `Transaction`**
  - Attributs : `id`, `montant`, `dateTransaction`, `description`
  - Méthodes abstraites : `valider()`, `exécuter()`

### 1.2 Héritage
**Objectif** : Créer une hiérarchie logique et réutilisable

Compte (abstrait) ├── CompteChequiers ├── CompteEpargne └── CompteTitre

Personnel (abstrait) ├── Employe ├── Manager └── Directeur

Transaction (abstrait) ├── Depot ├── Retrait ├── Virement └── Paiement

Code

**Classes concrètes :**
- `CompteChequiers` : Compte courant avec découvert autorisé
- `CompteEpargne` : Compte d'épargne avec intérêts mensuels
- `CompteTitre` : Compte de placement avec investissements
- `Client` : Titulaire de compte
- `Employe` : Personnel de base
- `Manager` : Superviseur d'agence
- `Directeur` : Responsable de la banque

### 1.3 Interfaces
**Objectif** : Définir des contrats pour les capacités spéciales

```java
interface Assurable {
    void souscrireAssurance();
    void resilerAssurance();
    double calculerPrimeAssurance();
}

interface Placable {
    void investir(double montant);
    void retirer(double montant);
    double obtenirRendement();
}

interface Auditable {
    void enregistrerAction(String action);
    List<String> obtenirHistorique();
}

interface Authentifiable {
    boolean authentifier(String motDePasse);
    void changerMotDePasse(String ancien, String nouveau);
}

interface Notifiable {
    void envoyerNotification(String message);
    void afficherNotification();
}
1.4 Polymorphisme
Objectif : Manipuler des listes d'objets via leur classe parente ou interface

Java
// Exemple 1 : Polymorphisme via héritage
List<Compte> comptes = new ArrayList<>();
comptes.add(new CompteChequiers(...));
comptes.add(new CompteEpargne(...));
comptes.add(new CompteTitre(...));

for (Compte compte : comptes) {
    compte.calculerInteret();      // Appel polymorphe
    compte.appliquerFrais();       // Différent pour chaque type
    compte.afficherDetails();      // Implémentation spécifique
}

// Exemple 2 : Polymorphisme via interface
List<Assurable> assurables = new ArrayList<>();
assurables.add(compteChequiers);
assurables.add(compteTitre);

for (Assurable a : assurables) {
    a.souscrireAssurance();       // Comportement polymorphe
}

// Exemple 3 : Polymorphisme avec Personnel
List<Personnel> equipe = new ArrayList<>();
equipe.add(new Employe(...));
equipe.add(new Manager(...));
equipe.add(new Directeur(...));

for (Personnel p : equipe) {
    System.out.println(p.calculerBonus());  // Calcul différent par rôle
}
Cas d'usage :

Calcul des intérêts différent selon le type de compte
Frais différents selon le type de client
Bonus variable selon la fonction du personnel
Notifications polymorphes (email, SMS, notification push)
1.5 Interface Graphique avec Swing
Objectif : Créer une GUI ergonomique et fonctionnelle

Fenêtres principales :

FenetrePrincipale : Menu d'accueil avec authentification
FenetreGestionComptes : Consultation et gestion des comptes
FenetreTransactions : Effectuer des opérations (dépôt, retrait, virement)
FenetreGestionClients : Ajouter/modifier/supprimer des clients
FenetreGestionPersonnel : Gestion du personnel (admin uniquement)
FenetreRapports : Consultation des statistiques et historiques
FenetreParametres : Configuration de l'application
Composants Swing utilisés :

JFrame : Fenêtres principales
JPanel : Panneaux de disposition
JButton : Boutons d'action
JTextField, JPasswordField : Champs de saisie
JTable : Affichage des données tabulaires
JComboBox : Listes déroulantes
JLabel, JTextArea : Affichage d'informations
JMenuBar, JMenu, JMenuItem : Menus
JDialog : Boîtes de dialogue
JFileChooser : Sélection de fichiers
2️⃣ Rigueur de Codage
2.1 Modificateurs d'Accès
Principe : Encapsulation stricte - rien public par défaut

Java
public abstract class Compte {
    // Attributs : PRIVATE (jamais de données brutes accessibles)
    private String numeroCompte;
    private double solde;
    private Client titulaire;
    private LocalDateTime dateCreation;
    
    // Constructeur : PROTECTED (accessible aux sous-classes)
    protected Compte(String numeroCompte, Client titulaire) {
        this.numeroCompte = numeroCompte;
        this.titulaire = titulaire;
        this.solde = 0.0;
        this.dateCreation = LocalDateTime.now();
    }
    
    // Getter : PUBLIC (accès contrôlé en lecture)
    public double getSolde() {
        return this.solde;
    }
    
    // Setter : PRIVATE ou PROTECTED (modification contrôlée)
    protected void setSolde(double montant) {
        if (montant >= 0) {
            this.solde = montant;
        }
    }
    
    // Méthodes utilitaires : PROTECTED (pour les sous-classes)
    protected void ajouterAuSolde(double montant) {
        this.solde += montant;
    }
    
    protected void retirerDuSolde(double montant) throws SoldeInsufficientException {
        if (montant > this.solde) {
            throw new SoldeInsufficientException("Solde insuffisant");
        }
        this.solde -= montant;
    }
    
    // Méthodes abstraites : PUBLIC (contrat pour sous-classes)
    public abstract void calculerInteret();
    public abstract void appliquerFrais();
    public abstract void afficherDetails();
}
Règles strictes :

✅ private : Données sensibles, implémentation interne
✅ protected : Héritage et accès contrôlé
✅ public : Interface publique de la classe
❌ Jamais d'attributs public
❌ Jamais d'accesseurs public qui retournent des références mutables
2.2 Mot-clé final
Objectif : Sécuriser les constantes et empêcher les redéfinitions dangereuses

Java
public final class BanqueManager {
    // Constantes : PUBLIC FINAL STATIC
    public static final double TAUX_INTERET_COMPTE_COURANT = 0.0;
    public static final double TAUX_INTERET_COMPTE_EPARGNE = 0.025;
    public static final double TAUX_INTERET_COMPTE_TITRE = 0.04;
    
    public static final double FRAIS_COMPTE_COURANT = 5.0;
    public static final double FRAIS_COMPTE_EPARGNE = 2.0;
    
    public static final int SOLDE_MINIMUM_COMPTE_EPARGNE = 100;
    public static final double DECOUVERTE_AUTORISE = 500.0;
    
    // Constantes de configuration
    public static final String NOM_BANQUE = "MaBank";
    public static final String VERSION = "1.0.0";
    
    // Classe finale : impossible de l'étendre
    private BanqueManager() {
        throw new AssertionError("Classe utilitaire, ne doit pas être instanciée");
    }
}

public abstract class Compte {
    // Attributs finaux : garantir l'immuabilité
    private final String numeroCompte;
    private final Client titulaire;
    private final LocalDateTime dateCreation;
    
    protected Compte(String numeroCompte, Client titulaire) {
        this.numeroCompte = numeroCompte;
        this.titulaire = titulaire;
        this.dateCreation = LocalDateTime.now();
    }
    
    // Méthode finale : empêcher la redéfinition
    public final String obtenirNumeroCompte() {
        return this.numeroCompte;
    }
    
    // Méthode finale : contrat sécurisé
    public final void verserAuCompte(double montant) throws MontantInvalidException {
        if (montant <= 0) {
            throw new MontantInvalidException("Le montant doit être positif");
        }
        this.effectuerDepot(montant);  // Appel polymorphe à une méthode abstraite
    }
    
    // Méthode abstraite que les sous-classes DOIVENT implémenter
    protected abstract void effectuerDepot(double montant);
}

public class CompteChequiers extends Compte {
    private final double decouvertAutorise = BanqueManager.DECOUVERTE_AUTORISE;
    
    // Impossible de redéfinir verserAuCompte() car elle est final
    
    @Override
    protected void effectuerDepot(double montant) {
        this.ajouterAuSolde(montant);
    }
}
Utilisation du final :

✅ Constantes public static final
✅ Attributs immuables private final
✅ Méthodes sensibles public final (API garantie)
✅ Classes utilitaires final (pas d'extension)
❌ Ne pas abuser : seulement quand c'est justifié
3️⃣ Logique et Flexibilité
3.1 Surcharge des Méthodes (Overloading)
Objectif : Proposer des variantes de paramètres pour plus de flexibilité

Java
public class Compte {
    // Surcharge 1 : Montant simple
    public void effectuerVirement(double montant, Compte compteDestination) 
            throws Exception {
        this.retirerDuSolde(montant);
        compteDestination.ajouterAuSolde(montant);
        this.enregistrerTransaction("Virement", montant);
    }
    
    // Surcharge 2 : Montant + description
    public void effectuerVirement(double montant, Compte compteDestination, String description) 
            throws Exception {
        effectuerVirement(montant, compteDestination);
        this.enregistrerTransaction("Virement : " + description, montant);
    }
    
    // Surcharge 3 : Montant + date programmée
    public void effectuerVirement(double montant, Compte compteDestination, LocalDateTime dateExecution) 
            throws Exception {
        if (dateExecution.isBefore(LocalDateTime.now())) {
            throw new DateInvalideException("La date ne peut pas être dans le passé");
        }
        // Programmation du virement
    }
    
    // Surcharge 4 : Montant + devise
    public void effectuerVirement(double montant, String devise, Compte compteDestination) 
            throws Exception {
        double montantConverti = convertir(montant, devise, compteDestination.getDevise());
        effectuerVirement(montantConverti, compteDestination);
    }
}

public class BanqueManager {
    // Surcharge 1 : Ajouter un client
    public void ajouterClient(String nom, String email) 
            throws ClientExisteException {
        Client client = new Client(nom, email);
        clients.add(client);
    }
    
    // Surcharge 2 : Ajouter un client avec adresse complète
    public void ajouterClient(String nom, String email, String adresse, String ville, String codePostal) 
            throws ClientExisteException {
        Client client = new Client(nom, email);
        client.setAdresse(adresse);
        client.setVille(ville);
        client.setCodePostal(codePostal);
        clients.add(client);
    }
    
    // Surcharge 3 : Ajouter un client avec numéro de téléphone
    public void ajouterClient(String nom, String email, String telephone) 
            throws ClientExisteException {
        Client client = new Client(nom, email);
        client.setTelephone(telephone);
        clients.add(client);
    }
    
    // Surcharge 4 : Ajouter un client à partir d'un objet existant
    public void ajouterClient(Client client) 
            throws ClientExisteException {
        if (clients.contains(client)) {
            throw new ClientExisteException("Ce client existe déjà");
        }
        clients.add(client);
    }
}

public class Transaction {
    // Surcharge 1 : Montant double
    public boolean valider(double montantMinimum) {
        return this.montant >= montantMinimum;
    }
    
    // Surcharge 2 : Montant et devise
    public boolean valider(double montantMinimum, String devise) {
        double convertedAmount = convertir(this.montant, this.devise, devise);
        return convertedAmount >= montantMinimum;
    }
    
    // Surcharge 3 : Montant et date
    public boolean valider(double montantMinimum, LocalDateTime dateLimit) {
        return this.montant >= montantMinimum && this.dateTransaction.isBefore(dateLimit);
    }
    
    // Surcharge 4 : Montant, devise, date
    public boolean valider(double montantMinimum, String devise, LocalDateTime dateLimit) {
        double convertedAmount = convertir(this.montant, this.devise, devise);
        return convertedAmount >= montantMinimum && this.dateTransaction.isBefore(dateLimit);
    }
}
3.2 Gestion des Exceptions
Objectif : Robustesse et anticipation des erreurs

Exceptions personnalisées :

Java
// Classe mère pour les exceptions métier
public abstract class BanqueException extends Exception {
    private LocalDateTime dateException;
    private String codeErreur;
    
    public BanqueException(String message, String codeErreur) {
        super(message);
        this.dateException = LocalDateTime.now();
        this.codeErreur = codeErreur;
    }
    
    public LocalDateTime getDateException() {
        return dateException;
    }
    
    public String getCodeErreur() {
        return codeErreur;
    }
}

// Exceptions spécifiques
public class SoldeInsufficientException extends BanqueException {
    public SoldeInsufficientException(String message) {
        super(message, "E001");
    }
}

public class ClientExisteException extends BanqueException {
    public ClientExisteException(String message) {
        super(message, "E002");
    }
}

public class CompteInexistantException extends BanqueException {
    public CompteInexistantException(String message) {
        super(message, "E003");
    }
}

public class MontantInvalidException extends BanqueException {
    public MontantInvalidException(String message) {
        super(message, "E004");
    }
}

public class AuthenticationException extends BanqueException {
    public AuthenticationException(String message) {
        super(message, "E005");
    }
}

public class OperationNonAutoriseeException extends BanqueException {
    public OperationNonAutoriseeException(String message) {
        super(message, "E006");
    }
}

public class DateInvalideException extends BanqueException {
    public DateInvalideException(String message) {
        super(message, "E007");
    }
}
Utilisation des exceptions :

Java
public class Compte {
    public void retirerDuSolde(double montant) throws SoldeInsufficientException {
        try {
            if (montant <= 0) {
                throw new MontantInvalidException("Le montant doit être positif");
            }
            
            if (montant > this.solde) {
                throw new SoldeInsufficientException(
                    String.format("Montant demandé : %.2f€, Solde disponible : %.2f€", 
                    montant, this.solde)
                );
            }
            
            this.solde -= montant;
            
        } catch (MontantInvalidException e) {
            System.err.println("[" + e.getCodeErreur() + "] " + e.getMessage());
            throw e;
        }
    }
}

public class BanqueManager {
    public void creerCompte(String typeCompte, Client client) 
            throws Exception {
        try {
            if (client == null) {
                throw new ClientExisteException("Le client ne peut pas être null");
            }
            
            if (!clients.contains(client)) {
                throw new CompteInexistantException("Le client n'existe pas dans la banque");
            }
            
            Compte compte;
            switch (typeCompte.toLowerCase()) {
                case "courant":
                    compte = new CompteChequiers(genererNumeroCompte(), client);
                    break;
                case "epargne":
                    compte = new CompteEpargne(genererNumeroCompte(), client);
                    break;
                case "titre":
                    compte = new CompteTitre(genererNumeroCompte(), client);
                    break;
                default:
                    throw new MontantInvalidException("Type de compte invalide");
            }
            
            comptes.add(compte);
            enregistrerAction(client.getNom() + " a créé un compte " + typeCompte);
            
        } catch (BanqueException e) {
            System.err.println("Erreur bancaire [" + e.getCodeErreur() + "]: " + e.getMessage());
            throw e;
        } catch (Exception e) {
            System.err.println("Erreur inattendue : " + e.getMessage());
            throw new BanqueException("Erreur lors de la création du compte", "E999") {};
        }
    }
}
Utilisation dans l'interface graphique :

Java
public class FenetreTransactions extends JFrame {
    private void effectuerVirement() {
        try {
            double montant = Double.parseDouble(textMontant.getText());
            Compte compteSource = (Compte) comboCompteSource.getSelectedItem();
            Compte compteDestination = (Compte) comboCompteDestination.getSelectedItem();
            
            if (montant <= 0) {
                JOptionPane.showMessageDialog(this, 
                    "Le montant doit être positif", 
                    "Erreur", 
                    JOptionPane.ERROR_MESSAGE);
                return;
            }
            
            compteSource.effectuerVirement(montant, compteDestination);
            JOptionPane.showMessageDialog(this, 
                "Virement effectué avec succès", 
                "Succès", 
                JOptionPane.INFORMATION_MESSAGE);
            
        } catch (SoldeInsufficientException e) {
            JOptionPane.showMessageDialog(this, 
                "Solde insuffisant : " + e.getMessage(), 
                "Erreur de solde", 
                JOptionPane.ERROR_MESSAGE);
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(this, 
                "Montant invalide. Veuillez entrer un nombre", 
                "Erreur de saisie", 
                JOptionPane.ERROR_MESSAGE);
        } catch (Exception e) {
            JOptionPane.showMessageDialog(this, 
                "Erreur lors du virement : " + e.getMessage(), 
                "Erreur", 
                JOptionPane.ERROR_MESSAGE);
            e.printStackTrace();
        }
    }
}
4️⃣ Organisation des Données
4.1 Framework Collections (Pas de Base de Données)
Objectif : Utiliser List, Map, Set pour la gestion des données en mémoire

Java
public class BanqueManager {
    // Collections pour stocker les données
    
    // Set : Clients uniques (pas de doublons)
    private Set<Client> clients;
    
    // List : Comptes (ordre d'insertion, peut avoir des doublons pour un client)
    private List<Compte> comptes;
    
    // Map : Transactions indexées par ID
    private Map<String, Transaction> transactions;
    
    // Map : Personnel indexé par ID employé
    private Map<String, Personnel> personnel;
    
    // List : Historique des actions (audit)
    private List<String> historique;
    
    // Map : Agrégations (statistiques par type de compte)
    private Map<String, Double> soldeParType;
    
    public BanqueManager() {
        this.clients = new HashSet<>();
        this.comptes = new ArrayList<>();
        this.transactions = new LinkedHashMap<>();  // Maintient l'ordre d'insertion
        this.personnel = new HashMap<>();
        this.historique = new ArrayList<>();
        this.soldeParType = new HashMap<>();
    }
    
    // Ajouter un client
    public void ajouterClient(Client client) throws ClientExisteException {
        if (clients.contains(client)) {
            throw new ClientExisteException("Ce client existe déjà");
        }
        clients.add(client);
    }
    
    // Rechercher un client par nom
    public Client rechercherClientParNom(String nom) {
        return clients.stream()
            .filter(c -> c.getNom().equalsIgnoreCase(nom))
            .findFirst()
            .orElse(null);
    }
    
    // Obtenir tous les comptes d'un client
    public List<Compte> obtenirComptesClient(Client client) {
        return comptes.stream()
            .filter(c -> c.getTitulaire().equals(client))
            .collect(Collectors.toList());
    }
    
    // Ajouter une transaction
    public void ajouterTransaction(Transaction transaction) {
        transactions.put(transaction.getId(), transaction);
    }
    
    // Obtenir les transactions dans une plage de dates
    public List<Transaction> obtenirTransactions(LocalDateTime debut, LocalDateTime fin) {
        return transactions.values().stream()
            .filter(t -> t.getDate().isAfter(debut) && t.getDate().isBefore(fin))
            .collect(Collectors.toList());
    }
    
    // Ajouter un membre du personnel
    public void ajouterPersonnel(Personnel personne) throws OperationNonAutoriseeException {
        if (personnel.containsKey(personne.getId())) {
            throw new OperationNonAutoriseeException("Cet employé existe déjà");
        }
        personnel.put(personne.getId(), personne);
    }
    
    // Obtenir le personnel par rôle
    public List<Personnel> obtenirPersonnelParRole(String role) {
        return personnel.values().stream()
            .filter(p -> p.getRole().equals(role))
            .collect(Collectors.toList());
    }
    
    // Calculer le solde total de tous les comptes
    public double calculerSoldeTotal() {
        return comptes.stream()
            .mapToDouble(Compte::getSolde)
            .sum();
    }
    
    // Obtenir les statistiques par type de compte
    public Map<String, Double> obtenirStatistiquesParType() {
        return comptes.stream()
            .collect(Collectors.groupingBy(
                c -> c.getClass().getSimpleName(),
                Collectors.summingDouble(Compte::getSolde)
            ));
    }
    
    // Enregistrer une action (historique)
    public void enregistrerAction(String action) {
        String entree = String.format("[%s] %s", LocalDateTime.now(), action);
        historique.add(entree);
    }
    
    // Obtenir l'historique complet
    public List<String> obtenirHistorique() {
        return new ArrayList<>(historique);
    }
    
    // Supprimer un client (et ses comptes)
    public void supprimerClient(Client client) {
        clients.remove(client);
        comptes.removeIf(c -> c.getTitulaire().equals(client));
        enregistrerAction("Client supprimé : " + client.getNom());
    }
}
Utilisation avancée des Collections :

Java
public class AnalyseFinanciere {
    private BanqueManager banque;
    
    // Obtenir les clients avec le plus grand solde total
    public List<Client> getClientsRiches(int top) {
        Map<Client, Double> soldeParClient = new HashMap<>();
        
        for (Compte compte : banque.obtenirTousLesComptes()) {
            Client client = compte.getTitulaire();
            soldeParClient.put(client, 
                soldeParClient.getOrDefault(client, 0.0) + compte.getSolde());
        }
        
        return soldeParClient.entrySet().stream()
            .sorted((a, b) -> Double.compare(b.getValue(), a.getValue()))
            .limit(top)
            .map(Map.Entry::getKey)
            .collect(Collectors.toList());
    }
    
    // Obtenir les transactions par type
    public Map<String, List<Transaction>> groupedByType() {
        return banque.obtenirToutesTransactions().stream()
            .collect(Collectors.groupingBy(t -> t.getClass().getSimpleName()));
    }
    
    // Vérifier l'unicité des numéros de compte
    public Set<String> obtenirNumeroComptesUniques() {
        return banque.obtenirTousLesComptes().stream()
            .map(Compte::getNumeroCompte)
            .collect(Collectors.toSet());
    }
}
Sérialisation et Sauvegarde (sans BD) :

Java
public class PersistanceManager {
    private static final String FICHIER_COMPTES = "comptes.dat";
    private static final String FICHIER_CLIENTS = "clients.dat";
    private static final String FICHIER_TRANSACTIONS = "transactions.dat";
    
    // Sauvegarder les données en fichier
    public static void sauvegarderDonnees(BanqueManager banque) {
        try {
            // Sauvegarder les clients
            ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream(FICHIER_CLIENTS));
            oos.writeObject(new ArrayList<>(banque.getClients()));
            oos.close();
            
            // Sauvegarder les comptes
            oos = new ObjectOutputStream(new FileOutputStream(FICHIER_COMPTES));
            oos.writeObject(new ArrayList<>(banque.getComptes()));
            oos.close();
            
            // Sauvegarder les transactions
            oos = new ObjectOutputStream(new FileOutputStream(FICHIER_TRANSACTIONS));
            oos.writeObject(new ArrayList<>(banque.getTransactions().values()));
            oos.close();
            
        } catch (IOException e) {
            System.err.println("Erreur lors de la sauvegarde : " + e.getMessage());
        }
    }
    
    // Charger les données depuis les fichiers
    public static void chargerDonnees(BanqueManager banque) {
        try {
            // Charger les clients
            ObjectInputStream ois = new ObjectInputStream(
                new FileInputStream(FICHIER_CLIENTS));
            @SuppressWarnings("unchecked")
            List<Client> clients = (List<Client>) ois.readObject();
            for (Client client : clients) {
                banque.ajouterClient(client);
            }
            ois.close();
            
            // Charger les comptes
            ois = new ObjectInputStream(new FileInputStream(FICHIER_COMPTES));
            @SuppressWarnings("unchecked")
            List<Compte> comptes = (List<Compte>) ois.readObject();
            for (Compte compte : comptes) {
                banque.ajouterCompte(compte);
            }
            ois.close();
            
        } catch (IOException | ClassNotFoundException e) {
            System.err.println("Erreur lors du chargement : " + e.getMessage());
        }
    }
}
📂 Structure du Projet
Code
bank-management-system/
├── src/
│   ├── modeles/
│   │   ├── Compte.java (abstrait)
│   │   ├── CompteChequiers.java
│   │   ├── CompteEpargne.java
│   │   ├── CompteTitre.java
│   │   ├── Client.java
│   │   ├── Personnel.java (abstrait)
│   │   ├── Employe.java
│   │   ├── Manager.java
│   │   ├── Directeur.java
│   │   ├── Transaction.java (abstrait)
│   │   ├── Depot.java
│   │   ├── Retrait.java
│   │   ├── Virement.java
│   │   └── Paiement.java
│   ├── interfaces/
│   │   ├── Assurable.java
│   │   ├── Placable.java
│   │   ├── Auditable.java
│   │   ├── Authentifiable.java
│   │   └── Notifiable.java
│   ├── exceptions/
│   │   ├── BanqueException.java
│   │   ├── SoldeInsufficientException.java
│   │   ├── ClientExisteException.java
│   │   ├── CompteInexistantException.java
│   │   ├── MontantInvalidException.java
│   │   ├── AuthenticationException.java
│   │   ├── OperationNonAutoriseeException.java
│   │   └── DateInvalideException.java
│   ├── gestionnaires/
│   │   ├── BanqueManager.java
│   │   ├── PersistanceManager.java
│   │   └── AnalyseFinanciere.java
│   ├── ui/
│   │   ├── FenetrePrincipale.java
│   │   ├── FenetreAuthentification.java
│   │   ├── FenetreGestionComptes.java
│   │   ├── FenetreTransactions.java
│   │   ├── FenetreGestionClients.java
│   │   ├── FenetreGestionPersonnel.java
│   │   ├── FenetreRapports.java
│   │   └── FenetreParametres.java
│   └── Application.java
├── PROJECT_SPECIFICATIONS.md
└── README.md
🎓 Critères d'Évaluation
Critère	Points	Vérification
Abstraction	15	Classes abstraites, pas d'instanciation directe
Héritage	15	Hiérarchie logique avec réutilisation de code
Interfaces	15	Contrats clairs, utilisation polymorphe
Polymorphisme	15	Manipulations de listes via classe parente
Encapsulation	10	Modificateurs d'accès respectés
Mot-clé final	5	Utilisation judicieuse
Surcharge	10	Variantes de paramètres pertinentes
Exceptions	10	Exceptions personnalisées, gestion robuste
Collections	10	List, Map, Set utilisées correctement
Interface Swing	10	GUI ergonomique et fonctionnelle
Lisibilité & Convention	5	Noms significatifs, code propre
Total : 120 points

✅ Points à Respecter Absolument
✔️ Aucune classe concrète ne peut être instanciée directement si elle est générique
✔️ Tous les attributs privés (pas d accès direct)
✔️ Utilisation cohérente des modificateurs (private/protected/public)
✔️ Au moins 3 interfaces avec du polymorphisme
✔️ Gestion d'exceptions personnalisées
✔️ Collections (List, Map, Set) pour toutes les données
✔️ Interface graphique Swing fonctionnelle
✔️ Surcharge de méthodes avec variantes pertinentes
✔️ Pas de base de données - Données en mémoire + sérialisation fichier
✔️ Code commenté et bien organisé
🚀 Prochaines Étapes
Créer la structure des dossiers
Implémenter les classes abstraites et interfaces
Développer les exceptions personnalisées
Créer le BanqueManager avec Collections
Implémenter l'interface Swing
Tester chaque fonctionnalité
Documenter le code
Bonne chance ! 💪