# Spring Boot Project - Gestion de Produits et de Patients

Ce projet Spring Boot a été développé avec **IntelliJ IDEA Ultimate**. Il permet la gestion de produits (et plus tard des patients, médecins, rendez-vous) à l'aide de **Spring Web**, **Spring Data JPA**, **Lombok** et **H2/MySQL**.

---

## 💪 Étapes de Mise en Place

### 1. Installation de l'IDE
- Télécharger et installer **IntelliJ IDEA Ultimate** : [https://www.jetbrains.com/idea/download/](https://www.jetbrains.com/idea/download/)

---

### 2. Création du Projet Spring Initializer
Créer un nouveau projet avec les dépendances suivantes :
- Spring Web
- Spring Data JPA
- H2 Database
- Lombok

---

### 3. Configuration de Lombok

Dans le fichier `pom.xml`, ajouter manuellement la version de Lombok pour éviter les erreurs avec les dernières versions de Java :

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.34</version> <!-- À ajouter -->
    <optional>true</optional>
</dependency>
```

Dans la section `<build><plugins>`, ajouter aussi :

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <configuration>
    <annotationProcessorPaths>
      <path>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.34</version>
      </path>
    </annotationProcessorPaths>
  </configuration>
</plugin>
```

---

### 4. Création de l'entité JPA `Product`

```java
@Entity
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;
    private int quantity;
}
```

---

### 5. Configuration de la base de données H2 dans `application.properties`

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
spring.jpa.show-sql=true
```

---

### 6. Création du Repository

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByNameContains(String keyword);
}
```

---

### 7. Opérations de Test (via `CommandLineRunner` ou Controller)

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @Bean
    CommandLineRunner start(ProductRepository repo) {
        return args -> {
            repo.save(new Product(null, "Ordinateur", 3500.0, 10));
            repo.save(new Product(null, "Smartphone", 2200.0, 5));

            repo.findAll().forEach(System.out::println);
        };
    }
}
```

---

### 8. Migration vers MySQL (au lieu de H2)

#### Modifier `application.properties` :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/produits_db
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
```

Assure-toi que MySQL est installé, la base `produits_db` existe, et que l'utilisateur a les droits.

---

### 9. Mapping des Associations (Patient, Médecin, Rendez-vous)

#### Exemple simplifié d'entités avec relations :

```java
@Entity
public class Patient {
    @Id @GeneratedValue
    private Long id;
    private String nom;
    
    @OneToMany(mappedBy = "patient")
    private List<RendezVous> rendezVous;
}

@Entity
public class Medecin {
    @Id @GeneratedValue
    private Long id;
    private String nom;

    @OneToMany(mappedBy = "medecin")
    private List<RendezVous> rendezVous;
}

@Entity
public class RendezVous {
    @Id @GeneratedValue
    private Long id;
    private LocalDateTime date;

    @ManyToOne
    private Patient patient;

    @ManyToOne
    private Medecin medecin;
}
```

---

## 📁 Objectifs futurs
- Ajouter des contrôleurs REST pour exposer les APIs
- Intégrer une interface web (Thymeleaf ou React)
- Gérer la sécurité avec Spring Security
- Déploiement sur un serveur distant (Heroku, Docker, etc.)

---

## 🚀 Auteur
Projet réalisé dans le cadre d'un apprentissage Spring Boot et JPA.

