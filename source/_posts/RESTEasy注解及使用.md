---
title: RESTEasy注解及使用
date: 2016-09-27 14:36:21
tags: [rest]
categories: JAX-RS
---

## RESTEasy简介
接上一篇[《JAX-RS规范的版本.实现.jar对应关系》](https://eternityrain.github.io/2016/09/27/JAX-RS%E8%A7%84%E8%8C%83%E7%9A%84%E7%89%88%E6%9C%AC.%E5%AE%9E%E7%8E%B0.jar%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB/)，我们知道RESTEasy是JAX-RS

### version:3.0.7.Final

| 注解             | 描述                                                                                                                                                                                                                                                                                     | 参数    |
|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| @ApplicationPath | 标注资源的root路径，加载子资源文件                                                                                                                                                                                                                                                       | value   |
| @Path            | 标注普通资源路径                                                                                                                                                                                                                                                                         | value   |
| @GET             | 获取                                                                                                                                                                                                                                                                                     |         |
| @POST            | 添加                                                                                                                                                                                                                                                                                     |         |
| @PUT             | 整体更新                                                                                                                                                                                                                                                                                 |         |
| @PATCH           | 部分更新(resteasy中并没有)                                                                                                                                                                                                                                                               |         |
| @DELETE          | 删除                                                                                                                                                                                                                                                                                     |         |
| @PathParam       | 资源路径参数                                                                                                                                                                                                                                                                             | value   |
| @QueryParam      | 资源请求参数                                                                                                                                                                                                                                                                             | value   |
| @MatrixParam     | 标注请求资源key-value类型的参数                                                                                                                                                                                                                                                          | value   |
| @FormParam       | 标注表单参数                                                                                                                                                                                                                                                                             | value   |
| @Encoded         | 标注需要UrlEncode的元素                                                                                                                                                                                                                                                                  |         |
| @Context         | 在参数列表注入系统级别参数javax.ws.rs.core.HttpHeaders, javax.ws.rs.core.UriInfo, javax.ws.rs.core.Request, javax.servlet.HttpServletRequest, javax.servlet.HttpServletResponse, javax.servlet.ServletConfig, javax.servlet.ServletContext, and javax.ws.rs.core.SecurityContext objects |         |
| @CookieParam     | 标注需要获取的cookie参数                                                                                                                                                                                                                                                                 | value   |
| @Consumes        | 指定client请求数据类型，与client的Content-Type匹配                                                                                                                                                                                                                                       | value[] |
| @Produces        | 指定client接收数据类型，与client的Accept匹配                                                                                                                                                                                                                                             | value[] |
| @HeaderParam     | 标注需要获取的header参数                                                                                                                                                                                                                                                                 | value   |
| @DefaultValue    | 标注默认值                                                                                                                                                                                                                                                                               | value   |
| @Form            | 标注表单对象，对象中的表单属性需要@FormParam进行标注                                                                                                                                                                                                                                     |         |

---

### 使用方法

#### @ApplicationPath

```

        @ApplicationPath("operations")
        public class OperationApplication extends Application {

        private Set<Object> singletons=new HashSet<Object>();

        public OperationApplication(){
            singletons.add(new Library());
            singletons.add(new BookStoreImpl());
        }

        @Override
        public Set<Object> getSingletons(){
            return singletons;
        }
    }
```

重写Application的getSingletons()方法，在构造器中加载Library和BookStoreImpl两份资源，只有在此添加的资源才能被访问

#### @Path,@GET,@PUT,@DELETE,@POST

```

	@Path("/library")
    public class Library {

        @GET
        //@PUT,@POST,@DELETE
        @Path("/books")
        public String getBooks(){
            return "book list";
        }
     }
```

@Path是类与方法级别 @GET,@PUT,@DELETE,@POST为方法级别

#### @PathParam,@QueryParam

···

	@PUT
	@Path("/book/{isbn}")
	public void addBook(@PathParam("isbn") String isbn, @QueryParam("name") String name){
	
	    System.out.println("add the book with name : "+name+", isbn : "+isbn);
	}
	
	@DELETE
	@Path("/book/{var: .*}/{isbn}")
	public void removeBook(@PathParam("isbn") String isbn,@PathParam("var") String var){
	        System.out.println(var);
	        System.out.println("the book with isbn : "+isbn+" has been removed");
	    }

···

通过声明pathparam参数名可以更方便的获取路径参数

```

    @POST
    @Path("/book{multi}")
    public void postBook(@PathParam("multi") PathSegment multi){
        MultivaluedMap<String,String> paramMap =multi.getMatrixParameters();
        Set<String> set=paramMap.keySet();

        for (String key:set){
            System.out.println(key+" : "+paramMap.get(key));
        }
    }
```

上一片段对应的请求为：

```

	POST http://localhost:8080/operations/library/book==;name=EJB 3.0;title=Bill Burke

```

通过PathSegment类的使用可以将路径参数进行map映射

#### @MatrixParam

```

    @PUT
    @Path("/books")
    public void updateBook(@MatrixParam("name") String name,@MatrixParam("title") String title){
        System.out.println("name :"+name+" ,title :"+title);
    }
```

使用@MatrixParam可以获取下面这种资源的请求参数：

```

	GET http://host.com/library/book;name=EJB 3.0;author=Bill Burke

```

#### @FormParam,@DefaultValue

```

    @POST
    @Path("/books-form1")
    public void addBookByForm(@FormParam("name") @DefaultValue("yimmm") String name,@FormParam("title") String title){
        System.out.println("name :"+name+" ,title :"+title);
    }

```

作为form提交的参数@FormParam可以根据名称进行获取

#### @Consumes

```

    @POST
    @Path("/books-form2")
    @Consumes(MediaType.APPLICATION_FORM_URLENCODED)
    public void addBookByForm(MultivaluedMap<String,String> form){
        Set<String> set=form.keySet();

        for (String key:set){
            System.out.println(key+" : "+form.get(key));
        }
    }
```

使用@Consumes进行请求类型的匹配，在进行form提交时，MultivaluedMap<String,String>可以一次性接收form的所有参数

#### @Form

```

    @POST
    @Path("/books-form3")
    @Consumes(MediaType.APPLICATION_FORM_URLENCODED)
    public void addBookByForm(@Form Book book){
        System.out.println(book.getAuthor());
        System.out.println(book.getTitle());
    }
```

使用@Form可以将form参数直接映射为对象 但是该对象需要进行相关标注

```

	public class Book {
    @FormParam("author")
    private String author;

    @FormParam("title")
    private String title;
```

#### @Context,@Encoded

```

    @GET
    @Path("encodeTest/{name}")
    public String testEncode(@Context javax.ws.rs.core.HttpHeaders headers,@PathParam("name") @Encoded String name){

        return name;
    }
```

@Context将javax.ws.rs.core.HttpHeaders进行嵌入，@Encoded对当前的pathparam name进行urlencode

#### @Produces

```

    @GET
    @Path("/books")
    @Produces({MediaType.APPLICATION_JSON,MediaType.TEXT_PLAIN})
    public String getBooks(){
        return "book list";
    }
```

@Produces与@Consumes用法相同，只不过是进行返回类型的匹配，它们的参数为字符串数组

---

**注：暂且说到这里，后续补充**