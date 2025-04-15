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
    List<Product> findByNameContains(String mc);
    List<Product> findByPriceGreaterThan(double price);

    @Query("select p from Product p where p.name like :x")
    List<Product> Search(@Param("x") String mc);

    @Query("select p from Product p where p.price>:x")
    List<Product> searchByPriceGreaterThan(@Param("x") double price);
}
```

---

### 7. Opérations de Test (via `CommandLineRunner` ou Controller)

```java
public class StudentsAppApplication implements CommandLineRunner {
	@Autowired
	private ProductRepository productRepository;
	public static void main(String[] args) {
		SpringApplication.run(StudentsAppApplication.class, args);
	}

	@Override
	public void run(String... args) throws Exception {
		//productRepository.save(new Product(null,"Comptuter",4300,3));
		//productRepository.save(new Product(null,"Printer",1200,4));
		//productRepository.save(new Product(null,"Smart Phone",3200,32));
		List<Product> products = productRepository.findAll();
		products.forEach(p->{System.out.println(p.toString());});
		Product product = productRepository.findById(1L).get();
		System.out.println("************************");
		System.out.println(product.getId());
		System.out.println(product.getName());
		System.out.println(product.getPrice());
		System.out.println(product.getQuantity());
		System.out.println("************************");
		// Update the product
		product.setName("Updated Computer");
		product.setPrice(4999);
		product.setQuantity(5);
		productRepository.save(product); // Save updated product

		System.out.println("After Update:");
		System.out.println(product);

		System.out.println("************************");


		List<Product> productList = productRepository.findByNameContains("C");
		productList.forEach(p->{System.out.println(p.toString());});

		System.out.println("************************");

		List<Product> productList2 = productRepository.Search("%C%");
		productList2.forEach(p->{System.out.println(p.toString());});

		System.out.println("************************");

		List<Product> productList3 = productRepository.searchByPriceGreaterThan(3000);
		productList3.forEach(p->{System.out.println(p.toString());});

		// Now delete the product
		//productRepository.deleteById(1L);
		//System.out.println("Product with ID 1 deleted.");
	}
}
```

---

### 8. Migration vers MySQL or SQL SERVER(au lieu de H2)

#### Modifier `application.properties` :

```properties
spring.application.name=JPA

# H2 désactivé
spring.h2.console.enabled=false

# Connexion à SQL Server Express
spring.datasource.url=jdbc:sqlserver://localhost\\SQLEXPRESS:1433;databaseName=users_db;encrypt=true;trustServerCertificate=true
spring.datasource.username=ton_utilisateur
spring.datasource.password=ton_mot_de_passe
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.SQLServerDialect

# Port du serveur Spring Boot
server.port=8083

---



## 📁 Objectifs futurs
- Ajouter des contrôleurs REST pour exposer les APIs
- Intégrer une interface web (Thymeleaf ou React)
- Gérer la sécurité avec Spring Security
- Déploiement sur un serveur distant (Heroku, Docker, etc.)

---

## 🚀 Auteur
Projet réalisé dans le cadre d'un apprentissage Spring Boot et JPA.

