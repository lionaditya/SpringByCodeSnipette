# 1. Crud operations
![alt text](image.png)![alt text](image-1.png)
## Things we encounter
### Adding Book
![alt text](image-2.png)
### See data in h2
![alt text](image-3.png)![alt text](image-4.png)
### Problem with save () method in PUT mapping
![alt text](image-5.png)![alt text](image-6.png)![alt text](image-7.png)![alt text](image-8.png)
### Coding
### Entity
```java
package com.example.BookApplication.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@Data  //This give both getter and setters
@NoArgsConstructor
@AllArgsConstructor
public class Book {

    @Id
    @GeneratedValue  //Automatically add this value and increment this value explicitly
    private Integer id;
    private String title;
    private String author;
    private String genre; // genre meaning type

}
```
### Repository
```java
package com.example.BookApplication.repository;

import com.example.BookApplication.entity.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository extends JpaRepository<Book,Integer> {

   public Book findBookByTitle(String title);
}
```
### Service
```java
package com.example.BookApplication.service;

import com.example.BookApplication.entity.Book;
import com.example.BookApplication.repository.BookRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service //Business Logic we add here so annotated with this annotation
public class BookService {

    @Autowired  //Also called as field injection -- on field you added @Autowired
    private BookRepository bookRepository;

    public Book addBook(Book book) {
         return bookRepository.save(book);
    }

    public Book getBookByName(String name) {

        return bookRepository.findBookByTitle(name);
    }

    public Book updateBook(Book book) {
        //save() method is used for add or update
         //But if we use it will create problem since
          // we are using @ID autoincrement

        return bookRepository.save(book);

    }

    public void deleteBook(Integer id) {

        bookRepository.deleteById(id);
    }
}
```
### Controller
```java
package com.example.BookApplication.controller;

import com.example.BookApplication.entity.Book;
import com.example.BookApplication.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController //Response body + Controller
@RequestMapping("/book/v1")  //Common api + version (we can handle from here also)
public class BookController {

   private final BookService bookService;
   //Once you add BookService as final we need to initialize via constructor

    @Autowired // It will inject the dependency - Constructor Injection
    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    //We are passing json to your application and it will converted into Entity automatically via @RequestBody
    //1. Add Api to add book inside our DB
    @PostMapping("/addBook")
    public ResponseEntity<Book> addBook(@RequestBody Book book){

        Book savedBook = bookService.addBook(book);
        return ResponseEntity.ok(savedBook);
    }


    @GetMapping("/getBook/{bookName}")
    public ResponseEntity<Book> getBookByName(@PathVariable("bookName") String name){

        Book bookByName = bookService.getBookByName(name);
        return ResponseEntity.ok(bookByName);
    }

    @PutMapping("/updateBook")
    public ResponseEntity<Book> updateBook(@RequestBody Book book){

        Book updateBook = bookService.updateBook(book);
        return ResponseEntity.ok(updateBook);
    }


    @DeleteMapping ("/deleteBook/{id}")
    public ResponseEntity<Book> deleteBook(@PathVariable("id") Integer id){

        bookService.deleteBook(id);
        return ResponseEntity.ok().build();
    }
 }
```
### App.prop
```properties

spring.h2.console.enabled=true
spring.datasource.username=root
spring.datasource.password=root12345
```
