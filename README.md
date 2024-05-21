# Exo_UML_Merise

## EXO Merise

Pour plus de détails, veuillez consulter le [document de devoir](Devoir%20persistence.docx).


## EXO UML 

### 1. Représenter le diagramme des cas d’utilisation de ce système

```plantuml
@startuml
left to right direction
set separator none

actor "Client" as Client
actor "Agence de Réservation" as Agence
actor "Gestionnaire de l'hôtel" as Gestionnaire

rectangle "Gestion Hôtelière" {
  usecase "Création et modification des hôtels et chambres" as G1
  usecase "Consultation des disponibililtés" as AC2
  usecase "Enregistrement des réservations immédiates" as AC3
  usecase "Enregistrement des arrhes" as AC4
  usecase "Enregistrement des consommations" as C5
  usecase "Etablissement de la facture au départ" as G7
  usecase "Consulté physiquement la liste des arrivées du jour" as G8
  usecase "Consulté physiquement la liste d'occupation des chambres" as G9
}

Client --> AC2
Client --> AC3
Client --> AC4
Client --> C5

Agence --> AC2
Agence --> AC3
Agence --> AC4

Gestionnaire --> G1
Gestionnaire --> G7
Gestionnaire --> G8
Gestionnaire --> G9

@enduml
```

![plantuml](plantuml.png).

### 2. Réalisez le diagramme de classes pour modéliser les éléments ci-dessus, en définissant les attributs et les méthodes (opérations) de chaque classe de ce diagramme, ainsi que les cardinalités des associations entre les classes.

```mermaid
classDiagram
    class Hotel {
        +int id
        +string nom
        +string adresse
        +string classe
        +int nbChambres
        +creerHotel()
        +modifierHotel()
        +creerChambre()
        +modifierChambre()
        +consulterDisponibilites()
        +consulterArrivees()
    }
    
    class Chambre {
        +int id
        +int numChambre
        +string categorie
        +int capacite
        +string degreConfort
        +float prix
        +string etat
        +modifierCategorie()
        +consulterEtat()
    }
    
    class Reservation {
        +int id
        +Date dateReservation
        +Date dateDebutSejour
        +Date dateFinSejour
        +int codeClient
        +int numChambre
        +float montantTotal
        +float arrhes
        +effectuerReservation()
        +confirmerReservation()
    }

    class Client {
        +int id
        +string nom
        +string adresse
        +string telephone
        +string email
        +effectuerReservation()
    }
    
    class Consommation {
        +int id
        +string description
        +float prix
        +Date date
        +enregistrerConsommation()
    }

    class Facture {
        +int id
        +Date dateEmission
        +float montantTotal
        +emmettreFacture()
    }

    Hotel "1" -- "0..*" Chambre
    Hotel "1" -- "0..*" Reservation
    Chambre "1" -- "0..*" Consommation
    Reservation "0..*" -- "1" Client
    Reservation "1" -- "0..*" Facture
    Reservation "1" -- "0..*" Chambre
```

### 3. Réalisez le diagramme d’activité du processus de réservation

```mermaid
flowchart TD
    A[Client souhaite faire une réservation] --> B[Recherche des disponibilités par le client en ligne]
    A --> B2[Recherche des disponibilités par un conseiller]
    B2 --> C
    B --> C[Affichage des disponibilités]
    C --> D[Sélection de la chambre]
    D --> E[Enregistrement de la réservation]
    E --> F[Confirmation avec arrhes]
    F --> G[Réservation confirmée]
```

### 4. Réalisez le diagramme de séquence du processus de réservation

```mermaid
sequenceDiagram
    participant Client
    participant Agence
    participant SystemeReservation
    participant Hotel

    Client ->> Agence: 1. Demande de réservation à un consiller
    Agence ->> SystemeReservation: 1. Vérifier disponibilités
    Client ->> SystemeReservation: 2. Demande de réservation en ligne
    SystemeReservation ->> Hotel: Rechercher chambres disponibles
    Hotel -->> SystemeReservation: Chambres disponibles
    SystemeReservation -->> Agence: 1. Afficher disponibilités
    Agence -->> Client: 1. Présenter les options disponibles
    SystemeReservation -->> Client: 2. Afficher disponibilités
    Client ->> Agence: 1. Sélectionner une chambre et confirmer
    Agence ->> SystemeReservation: 1. Enregistrer réservation
    Client ->> SystemeReservation: 2. Sélectionner une chambre et confirmer
    SystemeReservation ->> Hotel: Mettre à jour état des chambres
    SystemeReservation -->> Agence: 1. Réservation confirmée
    Agence -->> Client: 1. Confirmer la réservation
    SystemeReservation -->> Agence: 2. Réservation confirmée
```