# TourOptimizer Docker Variables


| Variable Name                        | Description                                                                 | Default Value                        | Example Value                        |
|--------------------------------------|-----------------------------------------------------------------------------|--------------------------------------|--------------------------------------|
| DNA_HAS_ELEMENT_LIMITS              | Enables/disables element limits                                             | false                                | true                                 |
| DNA_ELEMENT_LIMITS_COUNT            | Element limits count (takes action if `DNA_HAS_ELEMENT_LIMITS` is true)    | 100                                  | 150                                  |
| DNA_IS_DEBUG                        | Enables/disables debug mode                                                 | false                                | true                                 |
| DNA_USER                            | Security username                                                           | "dna"                                | "myusername"                         |
| DNA_PASSWORD                        | Security password                                                           | "jopt"                               | "mypassword"                         |
| DNA_SECURITY_ENABLED                | Enables/disables password protection                                        | false                                | true                                 |
| DNA_IP_WHITELIST                    | Comma-separated list of IP addresses for the whitelist                      | ""                                   | "127.0.0.1,192.168.1.100"             |
| DNA_SERVER_URL                      | Server URL                                                                  | "/"                                  | "/myserverurl"                       |
| DNA_API_DOCS_PATH                   | API docs path                                                               | "/v3/api-docs"                       | "/custom/api-docs"                   |
| DNA_UI_CONFIG_URL                   | UI config URL                                                               | "/v3/api-docs/swagger-config"        | "/custom/api-docs/swagger-config"    |
| DNA_UI_URL                          | UI URL                                                                      | "/v3/api-docs"                       | "/custom/api-docs"                   |
| DNA_WEBJARS_PREFIX                  | WebJars prefix                                                              | "/webjars"                           | "/custom/webjars"                    |
| DNA_DEFAULT_LIC                     | Default license                                                             | ""                                   | "my-default-license"                 |
| DNA_DATABASE_ACTIVE                 | Enables/disables MongoDB database                                           | false                                 | true                                |
| DNA_DATABASE_DOWNLOAD_ACTIVE        | Enables/disables MongoDB database download                                  | true                                 | false                                |
| DNA_DATABASE_CLEAN_SECONDS          | Database cleanup rate in seconds                                            | 7200                                 | 3600                                 |
| DNA_DATABASE_URI                    | MongoDB connection URI                                                      | "mongodb://dnauser:dnapwd@localhost:27017/admin" | "mongodb://myuser:mypwd@localhost:27017/myadmin" |
| DNA_DATABASE_DB                     | MongoDB database name                                                       | "touroptimizer"                      | "mydbname"                           |

Remember to replace the example values with the appropriate values for your specific setup when starting the Docker container. When skipping the value, the default value will be used.