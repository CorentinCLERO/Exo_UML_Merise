# Exo_UML_Merise

## EXO Merise

### 1. lister les données nécessaires

Dépêches AFP (DepêcheAFP)
- id : Identifiant unique de la dépêche (Integer)
- titre : Titre de la dépêche (String)
- contenu : Contenu de la dépêche (Text)
- date : Date de publication de la dépêche (Date)
- source : Source de la dépêche, par défaut "AFP" (String)
- categorie : Catégorie de la dépêche (politique, économie, sport, etc.) (String)


Articles Générés (ArticleGenere)
- id : Identifiant unique de l'article généré (Integer)
- titre : Titre de l'article généré (String)
- contenu : Contenu généré de l'article (Text)
- date : Date de génération de l'article (Date)
- categorie : Catégorie de l'article (String)
- tags : Tags associés à l'article (String)
- images : Liste des images associées à l'article (ListImages)
- videos : Liste des videos associées à l'article (ListVideos)

Sorties IA (SortieIA)
Classe abstraite représentant les différentes sorties générées par IA (texte, image, vidéo).
- id : Identifiant unique de la sortie IA (Integer)
- type : Type de sortie (texte, image, vidéo) (String)
- contenu : Contenu de la sortie IA (String)
- article : Référence à l'article généré auquel la sortie est associée (ArticleGenere)

Texte (Texte)
Sous-classe de SortieIA représentant un texte généré par IA.
- texte : Contenu textuel généré (String)

Image (Image)
Sous-classe de SortieIA représentant une image générée par IA.
- url : URL de l'image (String)
- description : Description de l'image (String)

Publicités (Publicite)
- id : Identifiant unique de la publicité (Integer)
- contenu : Contenu de la publicité (Text)
- url : URL de la publicité (String)
- article : Référence à l'article généré auquel la publicité est associée (ArticleGenere)

### 2. créer le diagramme de classe UML

```mermaid
classDiagram
    class DepêcheAFP {
        +Integer id
        +String titre
        +Text contenu
        +Date date
        +String source
        +String categorie
    }

    class ArticleGenere {
        +Integer id
        +String titre
        +Text contenu
        +Date date
        +String categorie
        +String tags
        +List~Images~ images
        +List~Videos~ videos
    }

    class SortieIA {
        <<abstract>>
        +Integer id
        +String type
        +String contenu
        +ArticleGenere article
    }

    class Texte extends SortieIA {
        +String texte
    }

    class Image extends SortieIA {
        +String url
        +String description
    }

    class Video extends SortieIA {
        +String url
        +String description
    }

    class Publicite {
        +Integer id
        +Text contenu
        +String url
        +ArticleGenere article
    }

    DepêcheAFP "1" --o "0..*" ArticleGenere : génère
    ArticleGenere "1" --o "0..*" Publicite : contient
    ArticleGenere "1" --o "0..*" SortieIA : produit
    SortieIA <|-- Texte
    SortieIA <|-- Image
    SortieIA <|-- Video
    Texte "1" --o "0..*" ArticleGenere : appartient
    Image "1" --o "0..*" ArticleGenere : appartient
    Video "1" --o "0..*" ArticleGenere : appartient
```

### 3. créer le diagramme MCD Merise

```mermaid
erDiagram
    DEPECHE_AFP {
        Integer id
        String titre
        Text contenu
        Date date
        String source
        String categorie
    }

    ARTICLE_GENERE {
        Integer id
        String titre
        Text contenu
        Date date
        String categorie
        String tags
    }

    SORTIE_IA {
        Integer id
        String type
        String contenu
    }

    TEXTE {
        Integer id
        String texte
    }

    IMAGE {
        Integer id
        String url
        String description
    }

    VIDEO {
        Integer id
        String url
        String description
    }

    PUBLICITE {
        Integer id
        Text contenu
        String url
    }

    DEPECHE_AFP ||--o{ ARTICLE_GENERE : "génère"
    ARTICLE_GENERE ||--o{ PUBLICITE : "contient"
    ARTICLE_GENERE ||--o{ SORTIE_IA : "produit"
    SORTIE_IA ||--|{ TEXTE : "extends"
    SORTIE_IA ||--|{ IMAGE : "extends"
    SORTIE_IA ||--|{ VIDEO : "extends"
    TEXTE ||--o| ARTICLE_GENERE : "appartient"
    IMAGE ||--o| ARTICLE_GENERE : "appartient"
    VIDEO ||--o| ARTICLE_GENERE : "appartient"
```

### 4. créer le schéma relationnel des tables grâce à un ORM dans le langage de votre choix

```
from sqlalchemy import create_engine, Column, Integer, String, Text, Date, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship, sessionmaker

Base = declarative_base()

class DepecheAFP(Base):
    __tablename__ = 'depeches_afp'
    id = Column(Integer, primary_key=True)
    titre = Column(String, nullable=False)
    contenu = Column(Text, nullable=False)
    date = Column(Date, nullable=False)
    source = Column(String, default="AFP")
    categorie = Column(String, nullable=False)
    articles = relationship("ArticleGenere", back_populates="depeche")

class ArticleGenere(Base):
    __tablename__ = 'articles_genere'
    id = Column(Integer, primary_key=True)
    titre = Column(String, nullable=False)
    contenu = Column(Text, nullable=False)
    date = Column(Date, nullable=False)
    categorie = Column(String, nullable=False)
    tags = Column(String)
    depeche_id = Column(Integer, ForeignKey('depeches_afp.id'))
    depeche = relationship("DepecheAFP", back_populates="articles")
    publicites = relationship("Publicite", back_populates="article")
    sortiesIA = relationship("SortieIA", back_populates="article")

class SortieIA(Base):
    __tablename__ = 'sorties_ia'
    id = Column(Integer, primary_key=True)
    type = Column(String, nullable=False)
    contenu = Column(Text, nullable=False)
    article_id = Column(Integer, ForeignKey('articles_genere.id'))
    article = relationship("ArticleGenere", back_populates="sortiesIA")

class Texte(SortieIA):
    __tablename__ = 'textes'
    id = Column(Integer, ForeignKey('sorties_ia.id'), primary_key=True)
    texte = Column(Text, nullable=False)

class Image(SortieIA):
    __tablename__ = 'images'
    id = Column(Integer, ForeignKey('sorties_ia.id'), primary_key=True)
    url = Column(String, nullable=False)
    description = Column(Text)

class Video(SortieIA):
    __tablename__ = 'videos'
    id = Column(Integer, ForeignKey('sorties_ia.id'), primary_key=True)
    url = Column(String, nullable=False)
    description = Column(Text)

class Publicite(Base):
    __tablename__ = 'publicites'
    id = Column(Integer, primary_key=True)
    contenu = Column(Text, nullable=False)
    url = Column(String)
    article_id = Column(Integer, ForeignKey('articles_genere.id'))
    article = relationship("ArticleGenere", back_populates="publicites")
```

## EXO UML HOTEL

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