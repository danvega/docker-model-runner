# Docker Model Runner with Spring AI

![Docker + Spring AI](https://img.shields.io/badge/Docker-Spring%20AI-blue)

## Unleash Local AI Models with Docker and Spring Boot

Looking to harness AI models locally without relying on cloud providers? This project demonstrates how to connect a Spring Boot application to Docker's Model Runner feature, allowing you to interact with AI models that run directly on your machine.

No API keys needed for OpenAI or other providers - your data stays local, and you control the entire AI interaction pipeline!

## Project Overview

This application showcases the integration between:

- Docker Desktop's Model Runner (Beta feature, requires Docker Desktop 4.40 or later)
- Spring Boot 3.4.4
- Spring AI framework
- Java 24

The app sends prompts to locally running AI models through Docker's OpenAI-compatible API endpoints, giving you full control over your AI interactions while benefiting from the Spring AI abstraction layer.

## Project Requirements

- Docker Desktop 4.40 or later (latest version required) with Apple Silicon (M1/M2/M3)
- Java 24 JDK
- Maven build tool
- Available RAM for model loading (requirements vary by model)

## Dependencies

The project relies on the following core dependencies:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-openai-spring-boot-starter</artifactId>
</dependency>
```

Spring AI BOM manages version compatibility:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>${spring-ai.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Getting Started

### 1. Enable Docker Model Runner

First, make sure the Docker Model Runner feature is enabled:

1. Open Docker Desktop
2. Navigate to Settings → Features in development (Beta tab)
3. Make sure "Enable Docker Model Runner" is checked
4. **Important:** Also check "Enable host-side TCP support" (leave the default port of 12434)
5. Apply and restart Docker Desktop

### 2. Pull an AI Model

Pull a model from Docker Hub to your local environment:

```bash
docker model pull ai/gemma3
```

You can check available models with:

```bash
docker model list
```

### 3. Configure Your Application

The application is pre-configured to connect to the Docker Model Runner API. Key configuration properties:

```properties
spring.ai.openai.api-key=_
spring.ai.openai.chat.base-url=http://localhost:12434/engines/llama.cpp
spring.ai.openai.chat.options.model=ai/gemma3
```

- The API key is set to "_" since no actual key is needed for local models
- The base URL points to the Docker Model Runner API endpoint
- The model name can be changed to any model you've pulled

## How to Run the Application

1. Ensure Docker Desktop is running and you've pulled a model
2. Build the application using Maven:

```bash
./mvnw clean package
```

3. Run the application:

```bash
./mvnw spring-boot:run
```

The application will start and automatically execute the `CommandLineRunner` bean, which sends a prompt to the AI model and prints the response.

## Relevant Code Examples

### Main Application Class

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    CommandLineRunner commandLineRunner(ChatClient.Builder builder) {
        return args -> {
            var client = builder.build();
            String response = client.prompt("When was Docker created?")
                    .call()
                    .content();

            System.out.println(response);
        };
    }
}
```

The code above:
1. Creates a Spring Boot application
2. Configures a `CommandLineRunner` that runs after startup
3. Uses Spring AI's `ChatClient` to send a prompt to the AI model
4. Prints the model's response to the console

## Troubleshooting

### Command Not Found

If `docker model` commands aren't recognized, create a symlink:

```bash
ln -s /Applications/Docker.app/Contents/Resources/cli-plugins/docker-model ~/.docker/cli-plugins/docker-model
```

### Memory Issues

Models require significant RAM. If your system becomes slow:
1. Try smaller models like `ai/gemma3`
2. Close other memory-intensive applications
3. Set memory limits in Docker Desktop settings

## Learn More

This project demonstrates a basic integration, but there's much more you can do:
- Build conversational interfaces with chat history
- Implement streaming responses for real-time feedback
- Create vector stores for long-term memory
- Enhance model responses with prompt engineering

## Conclusion

By combining Docker Model Runner with Spring AI, you get the best of both worlds: locally-run AI models with no external API dependencies, and Spring's powerful application framework.

This approach gives you complete control over your AI implementation while keeping data and processing local to your environment. Start experimenting with different models and build rich AI-powered applications right from your development machine!

Feel free to contribute to this project or use it as a starting point for your own AI applications.

Happy coding! 🐳 + 🤖 = 🚀