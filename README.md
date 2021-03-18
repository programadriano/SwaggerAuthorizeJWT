# .NET 5 Autenticação básica com swagger e JWT

Repositório contendo um exemplo de código que demonstra como trabalhar com uma autenticação básica no swagger.


### Implementações no código

Nesse repositório nós apenas atualizamos os arquivos `Startup.cs`, `Program.cs` e `appsettings.json`. A seguir vocês tem as alterações realizadas em cada um desses arquivos:

```Csharp
//Startup.cs

 public void ConfigureServices(IServiceCollection services)
        {            
            #region [Swagger]
            services.AddSwaggerGen(c =>
            {
                c.SwaggerDoc("v1", new OpenApiInfo { Title = "API", Version = "v1" });
                #region [Swagger Autenticação]
                //Adicionando uma definição para autenticação
                c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
                {
                    Name = "Autorização",
                    Type = SecuritySchemeType.ApiKey,
                    Scheme = "Bearer",
                    In = ParameterLocation.Header,
                    Description = "Autenticação básica utilizando Token JWT"
        
                });
                //passando qual sera o tipo de autentição
                c.AddSecurityRequirement(new OpenApiSecurityRequirement {
                   {
                     new OpenApiSecurityScheme
                     {
                       Reference = new OpenApiReference
                       {
                         Type = ReferenceType.SecurityScheme,
                         Id = "Bearer"
                       }
                      },
                      new string[] { }
                    }
                  });
                #endregion

            });
            #endregion

        }

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            //outras implementações
            app.UseAuthentication();
            //outras implementações           
        }
 
```
Estamos adicionando um middler para validação do nosso token.

```Csharp
//Program.cs
   .ConfigureServices((hostingContext, services) =>
              {
                  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddJwtBearer(options =>
                {
                    options.TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuerSigningKey = true,
                        IssuerSigningKey = new SymmetricSecurityKey(Encoding.ASCII
                            .GetBytes(hostingContext.Configuration.GetSection("JWTKey:secret").Value)),
                        ValidateIssuer = false,
                        ValidateAudience = false
                    };
                });
    
```
Adicionando a chave que iremos utilizar para validação do token.
```Csharp
//appsettings.json
,
  "JWTKey": {
    "secret": "batman batman batman"
  }
```