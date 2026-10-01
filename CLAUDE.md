# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

An e-commerce book store REST backend: books, stock quantity, users, orders. Spring Boot 2.0.5 (Maven, `mvnw` wrapper with Maven 3.5.4), Spring Data JPA, MySQL, `java.version` 1.8. The README links a Postman collection for the APIs: https://www.getpostman.com/collections/cdc815cf6dede16bfc27

## Architecture

Code is organized by feature package under `com`. Each package holds an entity, a `CrudRepository`, a `@Service` and a `@RestController`:

| Package | Entity → table | URL prefix |
|---|---|---|
| `com.controller` | `Book` → `booksavailable` | `/book/...` |
| `com.quantitycontroller` | `Quantity` → `quantity` | `/quantity/...` |
| `com.usercontroller` | `User` → `users` | `/user/...` (incl. `/user/loginform`) |
| `com.ordercontroller` | `Order` → `order1` | `/users/{userid}/order(s)...` |
| `com.orderplaced` | `OrderPlaced` → `orderplaced` | `/orderplaced/...` |
| `com.orderrelation` | `OrderRelation` → `orderrelation` | `/orderrelation/...` |

- `Order` is `@ManyToOne User` and `@ManyToMany Book` through the join table `order1_books`.
- `com.BookProjectApplication` is both the `@SpringBootApplication` (with explicit `scanBasePackages`) and a `@RestController`. Its `/manyorder/order/{orderid}` endpoint is a stub with its body commented out. **If you add a new feature package, also add it to `scanBasePackages`.**
- Endpoints use `@RequestMapping` with no method for reads and deletes, so delete endpoints respond to GET.

Database: MySQL `booksdb` on `localhost:3306`, `ddl-auto=update`. The `booksdb_*.sql` files at the repo root are MySQL dumps for seeding the tables.

## Commands

```bash
./mvnw clean package
./mvnw spring-boot:run
./mvnw test -Dtest=BookProjectApplicationTests
```

Caveat: `src/main/java/module-info.java` (uncommitted) declares `module Exam {}` and looks like it was copied from another Eclipse project. A named module with no `requires` breaks compilation against Spring, so delete it unless it was intended.
