PS：本项目是从我看到本次纳新要求才开始着手部署的，一个月内一边进行期末复习一边学习go web开发。
时间紧迫，又是第一次着手进行，项目的大部分内容仍处于一知半解的状态，几乎全程是跟着李文周老师的视频打下来的，感觉要学习的东西还有太多太多了。
希望俱乐部能通过我这份申请，接下来我会花更多时间学习的

#### swagger 接口文档

![image-20220614104536488](https://tva1.sinaimg.cn/large/e6c9d24ely1h37kubtr7tj21el0u0juy.jpg)

- swagger models(controllers.doc_models.go 中定义的返回数据结构体)
  ![image-20220614104839621](https://tva1.sinaimg.cn/large/e6c9d24ely1h37kxgm763j21kw0noafc.jpg)

  ![image-20220614104759247](https://tva1.sinaimg.cn/large/e6c9d24ely1h37kwrpenyj20u30u0q4d.jpg)


1. **Controller（控制器层）**：
   - 作为服务入口，接收客户端HTTP请求，首先对请求的路由进行精准匹配，判断请求应导向的具体业务逻辑处理模块。
   - 严格校验请求携带的参数，确保参数合法性与完整性，例如对数字型参数校验是否为有效数字等。
   - 在完成路由匹配和参数校验后，将请求转发至Service层，协调请求在整个服务中的流转。

2. **Service（服务层/逻辑层）**：
   - 是项目的核心业务逻辑处理区域，聚焦实现具体业务功能，如电商项目中的商品查询、下单、支付等操作。
   - 会依据业务需求调用Dao层的相关方法获取或存储数据，按照业务规则对数据进行处理、运算、判断等操作，最终将处理结果返回给Controller层，以便响应客户端。

3. **Dao（数据访问层）**：
   - **mysql部分**：
     - 通过引入“github.com/go-sql-driver/mysql”库，建立与MySQL数据库的连接，负责创建数据库连接池，执行各类SQL语句，包括SELECT查询、INSERT插入、UPDATE更新及DELETE删除语句等，同时妥善处理数据库连接的配置参数，如连接地址、用户名、密码、数据库名等，确保与MySQL数据库稳定交互。
   - **redis部分**：
     - 借助“github.com/go-redis/redis”库与Redis建立连接，常用于缓存热门数据以减轻数据库查询压力、存储临时状态信息等场景，可执行设置键值对、获取值、删除键等操作，提升系统响应速度。

4. **Logger（日志服务层）**：
   - **日志库使用**：采用功能强大的“go.uber.org/zap”日志库，能够按照DEBUG、INFO、WARN、ERROR等不同级别，详细记录项目运行过程中的各类信息，为开发调试与线上问题排查提供有力支持。
   - **日志文件切割**：结合“github.com/natefinch/lumberjack”库对日志文件进行切割管理，依据日期、大小等规则将日志文件分割，便于管理查看，有效避免单个日志文件过大带来的不便。
   - **中间件替换**：自定义中间件替换gin框架默认的中间件服务，可根据项目自身需求定制日志记录的格式、内容以及触发时机，使日志更精准反映项目运行关键信息。

5. **Settings（配置信息层）**：
   - 运用“github.com/spf13/viper”读取项目的配置文件“config.yaml”，该库支持多种格式的配置文件，如YAML、JSON等。它能将配置文件中的各项配置项按照定义的结构反序列化到对应的结构体中，方便项目各个模块便捷地获取所需的配置信息，涵盖数据库连接配置、服务运行的端口号、日志记录的相关配置等，实现配置的集中管理与灵活获取。

6. **Routers（路由层）**：
   - 负责精心定义整个Web应用的路由规则，明确各个URL路径对应的处理函数，也就是Controller中的相应方法。例如，定义“/api/products”路径对应获取商品列表的处理函数，“/api/products/{id}”对应获取单个商品详情的处理函数等，通过清晰的路由规则，将客户端的请求准确引导到对应的业务逻辑处理模块上，是构建Web服务接口的重要基础。

7. **Models（数据模型层）**：
   - 存放与数据相关的重要结构体定义，这些结构体在项目中发挥关键作用。一方面，用于接收前端传来的请求参数，例如前端在创建用户时发送包含用户名、密码等信息的JSON数据，后端会定义对应的用户结构体来接收并解析这些参数；另一方面，用于定义返回给前端的结果的数据格式，比如查询商品列表时，将从数据库获取的数据按照定义好的商品结构体格式进行整理，再返回给前端，确保前后端数据交互的一致性和规范性。

8. **pkg（工具包层）**：
   - **snowflake（雪花算法部分）**：利用“github.com/sony/sonyflake”实现雪花算法来生成用户ID等唯一标识符。在分布式系统中，需要保证生成的ID具有唯一性、有序性以及高效性等特点，雪花算法正好满足这些需求，它可以为每个新创建的用户或者其他需要唯一标识的实体生成一个全局唯一的ID，便于在数据库存储以及后续业务逻辑处理中进行准确的区分和识别。
   - **jwt（JSON Web Token部分）**：
     - 借助“github.com/dgrijalva/jwt-go”库来实现JWT（JSON Web Token）的生成和解析功能。JWT作为一种跨域认证解决方案，在前后端分离项目以及OAuth2.0业务场景中应用广泛。例如，在用户登录成功后，后端会生成一个包含用户相关信息（如用户ID、角色等）的JWT，并返回给前端，前端在后续的请求中携带这个JWT，后端通过解析该JWT来验证用户身份，实现无状态的认证机制，提高系统的安全性和可扩展性。

9. **docs（文档相关层）**：
   - 通过执行“swag init”命令生成swagger页面渲染所需的静态文件，包含“docs.go”、“swagger.json”和“swagger.yaml”等文件。Swagger是一个强大的API文档生成和可视化工具，通过这些文件可以自动生成清晰、直观的API文档界面，方便开发团队内部以及与外部协作时展示和查看各个API接口的详细信息，如接口地址、请求参数、返回结果等，提升项目的可维护性和可协作性。

10. **config.yaml（配置文件）**：
   - 这是项目的核心配置文件，集中存放了项目运行所需的各种配置信息，涵盖了从数据库连接相关的配置（如MySQL和Redis的连接地址、账号密码等），到服务运行的配置（如服务监听的端口号、运行模式等），再到日志记录的相关配置（如日志级别、日志文件存储路径等），是整个项目能够按照预期配置运行的关键所在，各个模块会从这里获取相应的配置参数来进行初始化和工作。

11. **web_app.log（日志文件）**：
   - 是项目运行过程中实际记录日志信息的文件，通过Logger层的相关配置和操作，将项目运行各个阶段产生的日志按照设定的格式和级别记录到该文件中，方便后续开发人员查看项目运行历史、排查问题等。

12. **main.go（项目主入口）**：
   - 作为整个项目的启动入口，承担着多项重要职责。首先，它会加载“config.yaml”文件中的配置信息，通过Settings层相关功能完成配置的读取和初始化，接着依次初始化各个组件，比如初始化数据库连接（调用Dao层相关初始化函数）、初始化日志服务（调用Logger层初始化函数）、注册路由（调用Routers层的注册函数）等，最后启动Web服务，开始监听指定端口，接收客户端的请求，开启整个项目的运行生命周期。

13. **Makefile（编译规则文件）**：
   - 定义了项目文件的编译规则，在项目编译过程中发挥了极大的便利作用。以往手动编译项目时，需要每次输入编译的命令以及对应的参数，过程繁琐且容易出错，而借助Makefile，开发人员无需再进行这些重复操作，只需要执行简单的“Make”命令（或者根据Makefile中定义的其他目标命令）即可按照预设的规则完成项目编译，同时它也确定了项目具体的编译流程，很多开源项目都会采用Makefile来规范编译过程，提高项目的构建效率和可维护性。