# Spring单元测试
## SpringBootTest
在SpringBoot中，提供了@SpringBootTest注解。当需要SpringBoot特性时，可以通过使用@SpringBootTest注解来作为@ContextConfiguration的替代。@SprngBootTest创建ApplicationContext，该context在test中被使用。
## @AfterAll
该注解标明的方法，会在所有Test执行完成之后再执行
> ### @AfterAll注解特性
> - @AfterAll注解的方法必须含有void类型返回值
> - @AfterAll注解标注方法不能为private
> - 默认情况下，@AfterAll注解的方法必须是static修饰的

> ### 在非static方法中标注@AfterAll
> 在非static方法上标注@AfterAll，需要在class上标注@TestInstance(TestInstance.Lifecycle.PER_CLASS)。  
> 因为默认情况下TestInstance的默认生命周期是PER_METHOD
> ```JAVA
> @TestInstance(TestInstance.Lifecycle.PER_CLASS)
>    public class BeforeAndAfterAnnotationsUnitTest {
> 
>        String input;
>        Long result;
>        @BeforeAll
>        public void setup() {
>            input = "77";
>        }
>
>        @AfterAll
>        public void teardown() {
>            input = null;
>            result = null;
>        }
>
>        @Test
>        public void whenConvertStringToLong_thenResultShouldBeLong() {
>            result = Long.valueOf(input);
>            Assertions.assertEquals(77l, result);
>        }
>    }
>    ```

## @AfterEach
该注解标明的方法，在每次@Test标注方法执行完成之后都会被执行。
> ### @AfterEach特性
> - 标注方法返回值为空
> - 标注方法不为private
> - 标注方法不是static

## @BeforeAll
类似@AfterAll
## @BeforeEach
类似@AfterEach