# springbootdemo
  springboot连接数据库&amp;模拟restful风格实现增删改查
  
  

  
  #配置文件
  ```
  server:
    port: 8080   配置端口号
    servlet:
      context-path: /hello   
  
  cupSize: A
  age: 18
  content: "cupSize: ${cupSize}, age: ${age}"
  ```
  #获取上述配置文件的信息
  ```
  @RestController
  public class HelloController {
      //将配置文件中的内容打印出来
      @Value("${cupSize}")
      private String cupSize;
      @Value("${age}")
      private int  age;
      @Value("${content}")
      private String content;
  //    @RequestMapping(value = {"/hello","hi"},method = RequestMethod.GET)
      //带参数
      @RequestMapping(value = "/hello/{id}")
      public String hello(@PathVariable(value = "id")Integer id){
  
  //        return content;
          return "id"+":"+id;
      }
  }
```
#  @Value("${age}")太过麻烦&另一种获取方式
```
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}


/*@Controller:处理http请求
* @RestController：Spring4之后加入的新注解，等同于：原来返回json需要@ResponseBody配合@Controller
* @RequestMapping:配置url映射
* */
@RestController
public class TestPersonController {
    //将配置文件的信息写到一个类里面去，这样比@Value()更为简便
    @Autowired
    private Person person;
    @RequestMapping(value = "testPerson")
    public String test(){
        return person.getName()+":"+person.getAge();
    }
}
```
  # spring 连接数据库的配置
   ```
  spring:
    datasource:
        driver-class-name: com.mysql.jdbc.Driver
        url: jdbc:mysql://localhost:3306/ssm
        username: root
        password: root
    jpa:
        hibernate:
          ddl-auto: create
        show-sql: true
   
   ```  

 #pom.xml添加一下依赖
 ```
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
</dependency>
 ```
 
 #Restful风格的CRUD
 ```
 //查
     @Autowired
     private UserRepository userRepository;
     @GetMapping(value = "/findAllUser")
     public List<User> findAllUser(){
         //spring-data-jpa操作数据库
         return userRepository.findAll();
     }
     //添加
     @PostMapping(value ="/addUser")
     public void
     addUser(@RequestParam(value = "username")String username,@RequestParam(value = "password") String password){
         User user=new User(3,username,password);
         userRepository.save(user);
     }
     //查一个
     @GetMapping(value = "/findAllUser/{id}")
     public User findOneUser(@PathVariable("id")Integer id){
         //spring-data-jpa操作数据库
         return userRepository.getOne(id);
     }
     //更新
     @PutMapping(value ="/updateUser/{id}")
     public User updateUser(@PathVariable("id")Integer id,
                            @RequestParam(value = "username")String username,@RequestParam(value = "password") String password){
         //spring-data-jpa操作数据库
         User user=new User(id,username,password);
         return userRepository.save(user);
     }
     //删除
     @DeleteMapping(value ="/deleteUser/{id}")
        public void deleteUser(@PathVariable("id")Integer id){
         userRepository.delete(findOneUser(id));
    }

 ```
#先前我们都是在dao层添加许多操作数据库的方法，但是用了、springboot之后，我们只需要继承一个接口即可
```
//dao层  封装好了许多方法，不用自己写sql语句
public interface UserRepository extends JpaRepository<User,Integer> {
}

```
