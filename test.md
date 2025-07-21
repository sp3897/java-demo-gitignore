<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>ordermodel-abac-portal</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    
    <name>OrderModel ABAC Portal</name>
    <description>WebSocket-based ABAC portal for Java object model visualization</description>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>
    
    <properties>
        <java.version>17</java.version>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    
    <dependencies>
        <!-- Spring Boot WebSocket -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
        
        <!-- Spring Boot Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <!-- Spring Boot Security (for ABAC) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        
        <!-- JSON Processing -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.fasterxml.jackson.datatype</groupId>
            <artifactId>jackson-datatype-jsr310</artifactId>
        </dependency>
        
        <!-- Validation -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        
        <!-- Database (if needed for ABAC permissions) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        
        <!-- Testing -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            
            <!-- Frontend Build Plugin -->
            <plugin>
                <groupId>com.github.eirslett</groupId>
                <artifactId>frontend-maven-plugin</artifactId>
                <version>1.15.0</version>
                <configuration>
                    <workingDirectory>frontend</workingDirectory>
                    <installDirectory>target</installDirectory>
                </configuration>
                <executions>
                    <execution>
                        <id>install node and npm</id>
                        <goals>
                            <goal>install-node-and-npm</goal>
                        </goals>
                        <configuration>
                            <nodeVersion>v18.17.0</nodeVersion>
                            <npmVersion>9.6.7</npmVersion>
                        </configuration>
                    </execution>
                    <execution>
                        <id>npm install</id>
                        <goals>
                            <goal>npm</goal>
                        </goals>
                        <configuration>
                            <arguments>install</arguments>
                        </configuration>
                    </execution>
                    <execution>
                        <id>npm run build</id>
                        <goals>
                            <goal>npm</goal>
                        </goals>
                        <configuration>
                            <arguments>run build</arguments>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            
            <!-- Copy Frontend Build -->
            <plugin>
                <artifactId>maven-resources-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy-resources</id>
                        <phase>process-classes</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${basedir}/target/classes/static</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>frontend/build</directory>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>



# OrderModel ABAC Portal

A WebSocket-based ABAC (Attribute-Based Access Control) portal for visualizing Java object models with real-time updates.

## Features
- Real-time Java object model visualization via WebSocket
- Interactive tree structure display
- Attribute-level access control (ABAC)
- Property descriptions and metadata
- React-based frontend with modern UI
- Spring Boot backend with WebSocket support

## Prerequisites
- Java 17+
- Maven 3.8+
- Node.js 18+ (automatically installed by frontend plugin)

## Quick Start

### 1. Clone/Download the project
### 2. Build and Run


package com.example.abac;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class OrderModelApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderModelApplication.class, args);
    }
}


package com.example.abac.config;

import com.example.abac.handler.ObjectModelVisualizationHandler;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Autowired
    private ObjectModelVisualizationHandler visualizationHandler;

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(visualizationHandler, "/api/objectmodel-viz")
                .setAllowedOrigins("*");
    }
}


package com.example.abac.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Description {
    String value();
    String category() default "general";
    String example() default "";
    boolean sensitive() default false;
}


package com.example.abac.model;

import com.example.abac.annotation.Description;
import com.fasterxml.jackson.annotation.JsonProperty;

import java.time.LocalDateTime;
import java.util.List;

public class OrderModel {
    
    @Description(value = "Unique identifier for the order", example = "ORD-12345")
    @JsonProperty("orderId")
    private String orderId;
    
    @Description(value = "Name of the customer who placed the order", sensitive = true, example = "John Doe")
    @JsonProperty("customerName")
    private String customerName;
    
    @Description(value = "Date and time when the order was placed", example = "2024-01-15T10:30:00")
    @JsonProperty("orderDate")
    private LocalDateTime orderDate;
    
    @Description(value = "List of products in this order", category = "products")
    @JsonProperty("products")
    private List<Product> products;
    
    @Description(value = "Total amount of the order", sensitive = true, example = "299.99")
    @JsonProperty("totalAmount")
    private double totalAmount;
    
    @Description(value = "Current status of the order", example = "PROCESSING")
    @JsonProperty("status")
    private OrderStatus status;
    
    // Constructors
    public OrderModel() {}
    
    public OrderModel(String orderId, String customerName, List<Product> products, 
                     double totalAmount, OrderStatus status) {
        this.orderId = orderId;
        this.customerName = customerName;
        this.orderDate = LocalDateTime.now();
        this.products = products;
        this.totalAmount = totalAmount;
        this.status = status;
    }
    
    // Getters and setters
    public String getOrderId() { return orderId; }
    public void setOrderId(String orderId) { this.orderId = orderId; }
    
    public String getCustomerName() { return customerName; }
    public void setCustomerName(String customerName) { this.customerName = customerName; }
    
    public LocalDateTime getOrderDate() { return orderDate; }
    public void setOrderDate(LocalDateTime orderDate) { this.orderDate = orderDate; }
    
    public List<Product> getProducts() { return products; }
    public void setProducts(List<Product> products) { this.products = products; }
    
    public double getTotalAmount() { return totalAmount; }
    public void setTotalAmount(double totalAmount) { this.totalAmount = totalAmount; }
    
    public OrderStatus getStatus() { return status; }
    public void setStatus(OrderStatus status) { this.status = status; }
    
    public static class Product {
        @Description(value = "Unique product identifier", example = "P001")
        @JsonProperty("productId")
        private String productId;
        
        @Description(value = "Name of the product", example = "MacBook Pro")
        @JsonProperty("productName")
        private String productName;
        
        @Description(value = "Price per unit of the product", sensitive = true, example = "1299.99")
        @JsonProperty("price")
        private double price;
        
        @Description(value = "Quantity of this product ordered", example = "2")
        @JsonProperty("quantity")
        private int quantity;
        
        @Description(value = "Category of the product", example = "Electronics")
        @JsonProperty("category")
        private String category;
        
        // Constructors
        public Product() {}
        
        public Product(String productId, String productName, double price, 
                      int quantity, String category) {
            this.productId = productId;
            this.productName = productName;
            this.price = price;
            this.quantity = quantity;
            this.category = category;
        }
        
        // Getters and setters
        public String getProductId() { return productId; }
        public void setProductId(String productId) { this.productId = productId; }
        
        public String getProductName() { return productName; }
        public void setProductName(String productName) { this.productName = productName; }
        
        public double getPrice() { return price; }
        public void setPrice(double price) { this.price = price; }
        
        public int getQuantity() { return quantity; }
        public void setQuantity(int quantity) { this.quantity = quantity; }
        
        public String getCategory() { return category; }
        public void setCategory(String category) { this.category = category; }
    }
    
    public enum OrderStatus {
        PENDING, PROCESSING, SHIPPED, DELIVERED, CANCELLED
    }
}


package com.example.abac.model;

import com.fasterxml.jackson.annotation.JsonProperty;
import java.util.List;

public class PropertyMetadata {
    @JsonProperty("name")
    private String name;
    
    @JsonProperty("type")
    private String type;
    
    @JsonProperty("description")
    private String description;
    
    @JsonProperty("required")
    private boolean required;
    
    @JsonProperty("sensitive")
    private boolean sensitive;
    
    @JsonProperty("nested")
    private boolean nested;
    
    @JsonProperty("category")
    private String category;
    
    @JsonProperty("example")
    private String example;
    
    @JsonProperty("nestedProperties")
    private List<PropertyMetadata> nestedProperties;
    
    // Constructors
    public PropertyMetadata() {}
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getType() { return type; }
    public void setType(String type) { this.type = type; }
    
    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }
    
    public boolean isRequired() { return required; }
    public void setRequired(boolean required) { this.required = required; }
    
    public boolean isSensitive() { return sensitive; }
    public void setSensitive(boolean sensitive) { this.sensitive = sensitive; }
    
    public boolean isNested() { return nested; }
    public void setNested(boolean nested) { this.nested = nested; }
    
    public String getCategory() { return category; }
    public void setCategory(String category) { this.category = category; }
    
    public String getExample() { return example; }
    public void setExample(String example) { this.example = example; }
    
    public List<PropertyMetadata> getNestedProperties() { return nestedProperties; }
    public void setNestedProperties(List<PropertyMetadata> nestedProperties) { 
        this.nestedProperties = nestedProperties; 
    }
}


package com.example.abac.model;

import com.fasterxml.jackson.annotation.JsonProperty;
import java.util.List;
import java.util.Map;

public class ObjectModelMetadata {
    @JsonProperty("className")
    private String className;
    
    @JsonProperty("packageName")
    private String packageName;
    
    @JsonProperty("description")
    private String description;
    
    @JsonProperty("properties")
    private List<PropertyMetadata> properties;
    
    @JsonProperty("visualConfig")
    private Map<String, Object> visualConfig;
    
    // Constructors
    public ObjectModelMetadata() {}
    
    // Getters and setters
    public String getClassName() { return className; }
    public void setClassName(String className) { this.className = className; }
    
    public String getPackageName() { return packageName; }
    public void setPackageName(String packageName) { this.packageName = packageName; }
    
    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }
    
    public List<PropertyMetadata> getProperties() { return properties; }
    public void setProperties(List<PropertyMetadata> properties) { this.properties = properties; }
    
    public Map<String, Object> getVisualConfig() { return visualConfig; }
    public void setVisualConfig(Map<String, Object> visualConfig) { this.visualConfig = visualConfig; }
}



package com.example.abac.model;

import com.fasterxml.jackson.annotation.JsonProperty;
import java.time.LocalDateTime;
import java.util.List;

public class VisualizationPayload {
    @JsonProperty("type")
    private String type;
    
    @JsonProperty("models")
    private List<ObjectModelMetadata> models;
    
    @JsonProperty("timestamp")
    private LocalDateTime timestamp;
    
    @JsonProperty("clientId")
    private String clientId;
    
    // Constructors
    public VisualizationPayload() {}
    
    // Getters and setters
    public String getType() { return type; }
    public void setType(String type) { this.type = type; }
    
    public List<ObjectModelMetadata> getModels() { return models; }
    public void setModels(List<ObjectModelMetadata> models) { this.models = models; }
    
    public LocalDateTime getTimestamp() { return timestamp; }
    public void setTimestamp(LocalDateTime timestamp) { this.timestamp = timestamp; }
    
    public String getClientId() { return clientId; }
    public void setClientId(String clientId) { this.clientId = clientId; }
}


package com.example.abac.handler;

import com.example.abac.model.ObjectModelMetadata;
import com.example.abac.model.OrderModel;
import com.example.abac.model.VisualizationPayload;
import com.example.abac.service.ObjectModelIntrospectionService;
import com.example.abac.service.OrderService;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

import java.time.LocalDateTime;
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.CopyOnWriteArraySet;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

@Component
public class ObjectModelVisualizationHandler extends TextWebSocketHandler {
    
    private static final CopyOnWriteArraySet<WebSocketSession> sessions = new CopyOnWriteArraySet<>();
    private final ObjectMapper objectMapper = new ObjectMapper();
    private final ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
    
    @Autowired
    private ObjectModelIntrospectionService introspectionService;
    
    @Autowired
    private OrderService orderService;
    
    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        sessions.add(session);
        System.out.println("WebSocket connection established: " + session.getId());
        
        // Send initial object model metadata
        sendObjectModelMetadata(session);
        
        // Start periodic updates
        startPeriodicUpdates();
    }
    
    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        sessions.remove(session);
        System.out.println("WebSocket connection closed: " + session.getId());
    }
    
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        System.out.println("Received message: " + payload);
        
        if ("refresh".equals(payload)) {
            sendObjectModelMetadata(session);
        } else if ("order-data".equals(payload)) {
            sendOrderData(session);
        }
    }
    
    private void sendObjectModelMetadata(WebSocketSession session) {
        try {
            // Introspect OrderModel class
            ObjectModelMetadata orderMetadata = introspectionService.introspectModel(OrderModel.class);
            
            List<ObjectModelMetadata> models = Arrays.asList(orderMetadata);
            
            VisualizationPayload payload = new VisualizationPayload();
            payload.setType("MODEL_METADATA");
            payload.setModels(models);
            payload.setTimestamp(LocalDateTime.now());
            payload.setClientId(session.getId());
            
            String jsonPayload = objectMapper.writeValueAsString(payload);
            
            if (session.isOpen()) {
                session.sendMessage(new TextMessage(jsonPayload));
            }
        } catch (Exception e) {
            System.err.println("Error sending object model metadata: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private void sendOrderData(WebSocketSession session) {
        try {
            OrderModel currentOrder = orderService.getCurrentOrder();
            
            VisualizationPayload payload = new VisualizationPayload();
            payload.setType("ORDER_DATA");
            payload.setTimestamp(LocalDateTime.now());
            
            String jsonPayload = objectMapper.writeValueAsString(currentOrder);
            
            if (session.isOpen()) {
                session.sendMessage(new TextMessage(jsonPayload));
            }
        } catch (Exception e) {
            System.err.println("Error sending order data: " + e.getMessage());
        }
    }
    
    public void broadcastModelUpdate() {
        for (WebSocketSession session : sessions) {
            if (session.isOpen()) {
                sendObjectModelMetadata(session);
            }
        }
    }
    
    private void startPeriodicUpdates() {
        scheduler.scheduleAtFixedRate(() -> {
            if (!sessions.isEmpty()) {
                broadcastModelUpdate();
            }
        }, 10, 30, TimeUnit.SECONDS);
    }
}


package com.example.abac.handler;

import com.example.abac.model.ObjectModelMetadata;
import com.example.abac.model.OrderModel;
import com.example.abac.model.VisualizationPayload;
import com.example.abac.service.ObjectModelIntrospectionService;
import com.example.abac.service.OrderService;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

import java.time.LocalDateTime;
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.CopyOnWriteArraySet;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

@Component
public class ObjectModelVisualizationHandler extends TextWebSocketHandler {
    
    private static final CopyOnWriteArraySet<WebSocketSession> sessions = new CopyOnWriteArraySet<>();
    private final ObjectMapper objectMapper = new ObjectMapper();
    private final ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
    
    @Autowired
    private ObjectModelIntrospectionService introspectionService;
    
    @Autowired
    private OrderService orderService;
    
    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        sessions.add(session);
        System.out.println("WebSocket connection established: " + session.getId());
        
        // Send initial object model metadata
        sendObjectModelMetadata(session);
        
        // Start periodic updates
        startPeriodicUpdates();
    }
    
    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        sessions.remove(session);
        System.out.println("WebSocket connection closed: " + session.getId());
    }
    
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        System.out.println("Received message: " + payload);
        
        if ("refresh".equals(payload)) {
            sendObjectModelMetadata(session);
        } else if ("order-data".equals(payload)) {
            sendOrderData(session);
        }
    }
    
    private void sendObjectModelMetadata(WebSocketSession session) {
        try {
            // Introspect OrderModel class
            ObjectModelMetadata orderMetadata = introspectionService.introspectModel(OrderModel.class);
            
            List<ObjectModelMetadata> models = Arrays.asList(orderMetadata);
            
            VisualizationPayload payload = new VisualizationPayload();
            payload.setType("MODEL_METADATA");
            payload.setModels(models);
            payload.setTimestamp(LocalDateTime.now());
            payload.setClientId(session.getId());
            
            String jsonPayload = objectMapper.writeValueAsString(payload);
            
            if (session.isOpen()) {
                session.sendMessage(new TextMessage(jsonPayload));
            }
        } catch (Exception e) {
            System.err.println("Error sending object model metadata: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private void sendOrderData(WebSocketSession session) {
        try {
            OrderModel currentOrder = orderService.getCurrentOrder();
            
            VisualizationPayload payload = new VisualizationPayload();
            payload.setType("ORDER_DATA");
            payload.setTimestamp(LocalDateTime.now());
            
            String jsonPayload = objectMapper.writeValueAsString(currentOrder);
            
            if (session.isOpen()) {
                session.sendMessage(new TextMessage(jsonPayload));
            }
        } catch (Exception e) {
            System.err.println("Error sending order data: " + e.getMessage());
        }
    }
    
    public void broadcastModelUpdate() {
        for (WebSocketSession session : sessions) {
            if (session.isOpen()) {
                sendObjectModelMetadata(session);
            }
        }
    }
    
    private void startPeriodicUpdates() {
        scheduler.scheduleAtFixedRate(() -> {
            if (!sessions.isEmpty()) {
                broadcastModelUpdate();
            }
        }, 10, 30, TimeUnit.SECONDS);
    }
}



package com.example.abac.service;

import com.example.abac.annotation.Description;
import com.example.abac.model.ObjectModelMetadata;
import com.example.abac.model.PropertyMetadata;
import org.springframework.stereotype.Service;

import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.List;

@Service
public class ObjectModelIntrospectionService {
    
    public ObjectModelMetadata introspectModel(Class<?> modelClass) {
        ObjectModelMetadata metadata = new ObjectModelMetadata();
        metadata.setClassName(modelClass.getSimpleName());
        metadata.setPackageName(modelClass.getPackage().getName());
        metadata.setDescription("Object model for " + modelClass.getSimpleName());
        
        // Extract field metadata with descriptions
        Field[] fields = modelClass.getDeclaredFields();
        List<PropertyMetadata> properties = new ArrayList<>();
        
        for (Field field : fields) {
            PropertyMetadata property = new PropertyMetadata();
            property.setName(field.getName());
            property.setType(field.getType().getSimpleName());
            property.setDescription(extractDescription(field));
            property.setRequired(isRequired(field));
            property.setSensitive(isSensitive(field));
            property.setNested(isComplexType(field));
            property.setCategory(extractCategory(field));
            property.setExample(extractExample(field));
            
            if (property.isNested()) {
                property.setNestedProperties(introspectNestedObject(field.getType()));
            }
            
            properties.add(property);
        }
        
        metadata.setProperties(properties);
        return metadata;
    }
    
    private String extractDescription(Field field) {
        Description desc = field.getAnnotation(Description.class);
        return desc != null ? desc.value() : "No description available";
    }
    
    private boolean isRequired(Field field) {
        // Simple logic - you can enhance this based on validation annotations
        return field.getName().equals("orderId") || field.getName().equals("productId");
    }
    
    private boolean isSensitive(Field field) {
        Description desc = field.getAnnotation(Description.class);
        return desc != null && desc.sensitive();
    }
    
    private String extractCategory(Field field) {
        Description desc = field.getAnnotation(Description.class);
        return desc != null ? desc.category() : "general";
    }
    
    private String extractExample(Field field) {
        Description desc = field.getAnnotation(Description.class);
        return desc != null ? desc.example() : "";
    }
    
    private boolean isComplexType(Field field) {
        Class<?> type = field.getType();
        return !type.isPrimitive() && 
               !type.equals(String.class) && 
               !Number.class.isAssignableFrom(type) && 
               !type.equals(boolean.class) &&
               !type.isEnum();
    }
    
    private List<PropertyMetadata> introspectNestedObject(Class<?> nestedClass) {
        if (nestedClass.equals(List.class) || nestedClass.isArray()) {
            // Handle collections - this is simplified
            return new ArrayList<>();
        }
        
        Field[] fields = nestedClass.getDeclaredFields();
        List<PropertyMetadata> nestedProperties = new ArrayList<>();
        
        for (Field field : fields) {
            PropertyMetadata property = new PropertyMetadata();
            property.setName(field.getName());
            property.setType(field.getType().getSimpleName());
            property.setDescription(extractDescription(field));
            property.setSensitive(isSensitive(field));
            property.setExample(extractExample(field));
            
            nestedProperties.add(property);
        }
        
        return nestedProperties;
    }
}


package com.example.abac.service;

import com.example.abac.model.OrderModel;
import org.springframework.stereotype.Service;

import java.util.Arrays;
import java.util.List;
import java.util.Random;

@Service
public class OrderService {
    
    private final Random random = new Random();
    private OrderModel currentOrder;
    
    public OrderModel getCurrentOrder() {
        if (currentOrder == null) {
            currentOrder = generateSampleOrder();
        } else {
            // Simulate order updates
            updateOrderStatus();
        }
        return currentOrder;
    }
    
    private OrderModel generateSampleOrder() {
        List<OrderModel.Product> products = Arrays.asList(
            new OrderModel.Product("P001", "MacBook Pro 16\"", 2499.99, 1, "Electronics"),
            new OrderModel.Product("P002", "Wireless Mouse", 79.99, 2, "Accessories"),
            new OrderModel.Product("P003", "USB-C Hub", 149.99, 1, "Accessories"),
            new OrderModel.Product("P004", "Laptop Stand", 89.99, 1, "Accessories")
        );
        
        double total = products.stream()
            .mapToDouble(p -> p.getPrice() * p.getQuantity())
            .sum();
        
        return new OrderModel(
            "ORD-" + System.currentTimeMillis(),
            "John Doe",
            products,
            total,
            OrderModel.OrderStatus.PROCESSING
        );
    }
    
    private void updateOrderStatus() {
        // Simulate status changes
        OrderModel.OrderStatus[] statuses = OrderModel.OrderStatus.values();
        int currentIndex = Arrays.asList(statuses).indexOf(currentOrder.getStatus());
        
        if (currentIndex < statuses.length - 1 && random.nextBoolean()) {
            currentOrder.setStatus(statuses[currentIndex + 1]);
        }
        
        // Update total amount slightly (simulate price changes)
        if (random.nextFloat() < 0.1) { // 10% chance
            double currentTotal = currentOrder.getTotalAmount();
            double variation = (random.nextDouble() - 0.5) * 0.02; // +/- 1%
            currentOrder.setTotalAmount(currentTotal * (1 + variation));
        }
    }
}


package com.example.abac.service;

import org.springframework.stereotype.Service;

import java.util.HashSet;
import java.util.Set;

@Service
public class ABACService {
    
    // Simplified ABAC service for demonstration
    public Set<String> getApprovedAttributes(String clientId, String objectModel) {
        Set<String> approvedAttributes = new HashSet<>();
        
        // Default approved attributes
        approvedAttributes.add("orderId");
        approvedAttributes.add("orderDate");
        approvedAttributes.add("status");
        approvedAttributes.add("products.productId");
        approvedAttributes.add("products.productName");
        approvedAttributes.add("products.quantity");
        approvedAttributes.add("products.category");
        
        // Simulated client-specific approvals
        if ("premium-client".equals(clientId)) {
            approvedAttributes.add("customerName");
            approvedAttributes.add("totalAmount");
            approvedAttributes.add("products.price");
        }
        
        return approvedAttributes;
    }
    
    public boolean hasAttributeAccess(String clientId, String attributePath) {
        Set<String> approvedAttributes = getApprovedAttributes(clientId, "OrderModel");
        return approvedAttributes.contains(attributePath);
    }
}


package com.example.abac.service;

import org.springframework.stereotype.Service;

import java.util.HashSet;
import java.util.Set;

@Service
public class ABACService {
    
    // Simplified ABAC service for demonstration
    public Set<String> getApprovedAttributes(String clientId, String objectModel) {
        Set<String> approvedAttributes = new HashSet<>();
        
        // Default approved attributes
        approvedAttributes.add("orderId");
        approvedAttributes.add("orderDate");
        approvedAttributes.add("status");
        approvedAttributes.add("products.productId");
        approvedAttributes.add("products.productName");
        approvedAttributes.add("products.quantity");
        approvedAttributes.add("products.category");
        
        // Simulated client-specific approvals
        if ("premium-client".equals(clientId)) {
            approvedAttributes.add("customerName");
            approvedAttributes.add("totalAmount");
            approvedAttributes.add("products.price");
        }
        
        return approvedAttributes;
    }
    
    public boolean hasAttributeAccess(String clientId, String attributePath) {
        Set<String> approvedAttributes = getApprovedAttributes(clientId, "OrderModel");
        return approvedAttributes.contains(attributePath);
    }
}


package com.example.abac.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
    
    @GetMapping("/")
    public String index() {
        return "index.html";
    }
}


server:
  port: 8080
  servlet:
    context-path: /

spring:
  application:
    name: ordermodel-abac-portal
  
  websocket:
    servlet:
      allowed-origins: "*"
  
  security:
    basic:
      enabled: false
  
  h2:
    console:
      enabled: true
      path: /h2-console
  
  jpa:
    hibernate:
      ddl-auto: create-drop
    show-sql: false
    database-platform: org.hibernate.dialect.H2Dialect
  
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password: password

logging:
  level:
    com.example.abac: DEBUG
    org.springframework.web.socket: INFO
    org.springframework.security: INFO

abac:
  default-permissions:
    - orderId
    - orderDate
    - status
    - products.productId
    - products.productName
    - products.quantity
  sensitive-attributes:
    - customerName
    - totalAmount
    - products.price


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OrderModel ABAC Portal</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 2rem;
            text-align: center;
            margin-bottom: 2rem;
        }
        .status {
            display: inline-block;
            padding: 5px 10px;
            border-radius: 5px;
            margin-left: 10px;
            font-size: 0.8em;
        }
        .connected { background-color: #4CAF50; }
        .disconnected { background-color: #f44336; }
        .loading {
            text-align: center;
            padding: 2rem;
        }
        .object-model {
            background: white;
            border-radius: 8px;
            padding: 1.5rem;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            margin-bottom: 1rem;
        }
        .property {
            padding: 0.5rem;
            border-left: 3px solid #ddd;
            margin: 0.5rem 0;
        }
        .sensitive { border-left-color: #ff9800; }
        .nested { border-left-color: #2196f3; }
        pre {
            background: #f8f9fa;
            padding: 1rem;
            border-radius: 4px;
            overflow-x: auto;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>OrderModel ABAC Portal</h1>
        <p>Real-time Java Object Model Visualization</p>
        <div id="connectionStatus">
            <span id="statusText">Connecting...</span>
            <span id="statusIndicator" class="status disconnected">●</span>
        </div>
    </div>
    
    <div class="container">
        <div id="loading" class="loading">
            <p>Loading object model data...</p>
        </div>
        
        <div id="content" style="display: none;">
            <div id="objectModels"></div>
            <div id="orderData"></div>
        </div>
        
        <button onclick="refreshData()" style="padding: 10px 20px; margin: 10px;">Refresh Data</button>
    </div>

    <script>
        let ws;
        let connectionStatus = document.getElementById('statusText');
        let statusIndicator = document.getElementById('statusIndicator');
        let loading = document.getElementById('loading');
        let content = document.getElementById('content');
        let objectModels = document.getElementById('objectModels');
        let orderData = document.getElementById('orderData');

        function connectWebSocket() {
            ws = new WebSocket('ws://localhost:8080/api/objectmodel-viz');
            
            ws.onopen = function() {
                connectionStatus.textContent = 'Connected';
                statusIndicator.className = 'status connected';
                console.log('WebSocket connected');
            };
            
            ws.onmessage = function(event) {
                try {
                    const data = JSON.parse(event.data);
                    handleMessage(data);
                } catch (error) {
                    console.error('Error parsing message:', error);
                }
            };
            
            ws.onclose = function() {
                connectionStatus.textContent = 'Disconnected';
                statusIndicator.className = 'status disconnected';
                console.log('WebSocket disconnected');
                // Try to reconnect after 3 seconds
                setTimeout(connectWebSocket, 3000);
            };
            
            ws.onerror = function(error) {
                console.error('WebSocket error:', error);
                connectionStatus.textContent = 'Error';
                statusIndicator.className = 'status disconnected';
            };
        }

        function handleMessage(data) {
            loading.style.display = 'none';
            content.style.display = 'block';
            
            if (data.type === 'MODEL_METADATA') {
                displayObjectModels(data.models);
            } else if (data.orderId) {
                // This is order data
                displayOrderData(data);
            }
        }

        function displayObjectModels(models) {
            objectModels.innerHTML = '';
            
            models.forEach(model => {
                const modelDiv = document.createElement('div');
                modelDiv.className = 'object-model';
                
                modelDiv.innerHTML = `
                    <h2>${model.className}</h2>
                    <p><strong>Package:</strong> ${model.packageName}</p>
                    <p><strong>Description:</strong> ${model.description}</p>
                    
                    <h3>Properties:</h3>
                    <div class="properties">
                        ${model.properties.map(prop => `
                            <div class="property ${prop.sensitive ? 'sensitive' : ''} ${prop.nested ? 'nested' : ''}">
                                <strong>${prop.name}</strong> (${prop.type})
                                ${prop.sensitive ? '<span style="color: #ff9800;">🔒 Sensitive</span>' : ''}
                                ${prop.required ? '<span style="color: #f44336;">* Required</span>' : ''}
                                <br>
                                <small>${prop.description}</small>
                                ${prop.example ? `<br><em>Example: ${prop.example}</em>` : ''}
                            </div>
                        `).join('')}
                    </div>
                `;
                
                objectModels.appendChild(modelDiv);
            });
        }

        function displayOrderData(order) {
            orderData.innerHTML = `
                <div class="object-model">
                    <h2>Current Order Data</h2>
                    <pre>${JSON.stringify(order, null, 2)}</pre>
                </div>
            `;
        }

        function refreshData() {
            if (ws && ws.readyState === WebSocket.OPEN) {
                ws.send('refresh');
                ws.send('order-data');
            }
        }

        // Initialize WebSocket connection when page loads
        connectWebSocket();
        
        // Request order data after initial connection
        setTimeout(() => {
            if (ws && ws.readyState === WebSocket.OPEN) {
                ws.send('order-data');
            }
        }, 1000);
    </script>
</body>
</html>


{
  "name": "ordermodel-frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "^5.0.1",
    "antd": "^5.12.0",
    "@ant-design/icons": "^5.2.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "proxy": "http://localhost:8080"
}


import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import 'antd/dist/reset.css';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);


import React, { useState, useEffect } from 'react';
import { Layout, Typography, Card, Spin, Button, message } from 'antd';
import ObjectModelVisualizer from './components/ObjectModelVisualizer';
import OrderDisplay from './components/OrderDisplay';
import ConnectionStatus from './components/ConnectionStatus';
import './App.css';

const { Header, Content } = Layout;
const { Title, Paragraph } = Typography;

function App() {
  const [models, setModels] = useState([]);
  const [orderData, setOrderData] = useState(null);
  const [connectionStatus, setConnectionStatus] = useState('Disconnected');
  const [socket, setSocket] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    connectWebSocket();
    return () => {
      if (socket) {
        socket.close();
      }
    };
  }, []);

  const connectWebSocket = () => {
    const ws = new WebSocket('ws://localhost:8080/api/objectmodel-viz');
    
    ws.onopen = () => {
      console.log('WebSocket connected');
      setConnectionStatus('Connected');
      setSocket(ws);
      message.success('Connected to server');
    };
    
    ws.onmessage = (event) => {
      try {
        const data = JSON.parse(event.data);
        handleMessage(data);
      } catch (error) {
        console.error('Error parsing message:', error);
        message.error('Error processing server message');
      }
    };
    
    ws.onclose = () => {
      console.log('WebSocket disconnected');
      setConnectionStatus('Disconnected');
      message.warning('Connection lost. Attempting to reconnect...');
      setTimeout(connectWebSocket, 3000);
    };
    
    ws.onerror = (error) => {
      console.error('WebSocket error:', error);
      setConnectionStatus('Error');
      message.error('Connection error occurred');
    };
  };

  const handleMessage = (data) => {
    setLoading(false);
    
    if (data.type === 'MODEL_METADATA') {
      setModels(data.models || []);
    } else if (data.orderId) {
      setOrderData(data);
    }
  };

  const refreshData = () => {
    if (socket && socket.readyState === WebSocket.OPEN) {
      socket.send('refresh');
      socket.send('order-data');
      message.info('Refreshing data...');
    } else {
      message.error('Not connected to server');
    }
  };

  return (
    <Layout className="app-layout">
      <Header className="app-header">
        <div className="header-content">
          <Title level={2} style={{ color: 'white', margin: 0 }}>
            OrderModel ABAC Portal
          </Title>
          <Paragraph style={{ color: 'rgba(255,255,255,0.8)', margin: '8px 0 0 0' }}>
            Real-time Java Object Model Visualization with ABAC
          </Paragraph>
          <div className="header-controls">
            <ConnectionStatus status={connectionStatus} />
            <Button 
              type="primary" 
              ghost 
              onClick={refreshData}
              style={{ marginLeft: '16px' }}
            >
              Refresh Data
            </Button>
          </div>
        </div>
      </Header>
      
      <Content className="app-content">
        <div className="content-container">
          {loading ? (
            <Card className="loading-card">
              <Spin size="large" />
              <Paragraph style={{ textAlign: 'center', marginTop: '16px' }}>
                Loading object model data...
              </Paragraph>
            </Card>
          ) : (
            <div className="content-grid">
              <div className="model-section">
                <Card title="Object Model Structure" className="model-card">
                  <ObjectModelVisualizer models={models} />
                </Card>
              </div>
              
              <div className="data-section">
                <Card title="Live Order Data" className="data-card">
                  {orderData ? (
                    <OrderDisplay order={orderData} />
                  ) : (
                    <Paragraph>No order data available</Paragraph>
                  )}
                </Card>
              </div>
            </div>
          )}
        </div>
      </Content>
    </Layout>
  );
}

export default App;


.app-layout {
  min-height: 100vh;
}

.app-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 0;
}

.header-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 24px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  min-height: 120px;
}

.header-controls {
  display: flex;
  align-items: center;
  margin-top: 16px;
}

.app-content {
  background: #f5f5f5;
  padding: 24px;
}

.content-container {
  max-width: 1200px;
  margin: 0 auto;
}

.loading-card {
  text-align: center;
  padding: 48px;
}

.content-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  margin-top: 24px;
}

.model-card,
.data-card {
  height: fit-content;
}

@media (max-width: 768px) {
  .content-grid {
    grid-template-columns: 1fr;
  }
  
  .header-content {
    min-height: 100px;
    text-align: center;
  }
  
  .header-controls {
    justify-content: center;
  }
}


import React from 'react';
import { Badge, Space, Typography } from 'antd';

const { Text } = Typography;

const ConnectionStatus = ({ status }) => {
  const getStatusConfig = (status) => {
    switch (status) {
      case 'Connected':
        return { color: 'green', text: 'Connected' };
      case 'Disconnected':
        return { color: 'red', text: 'Disconnected' };
      case 'Error':
        return { color: 'orange', text: 'Error' };
      default:
        return { color: 'gray', text: 'Unknown' };
    }
  };

  const config = getStatusConfig(status);

  return (
    <Space>
      <Badge color={config.color} />
      <Text style={{ color: 'white' }}>
        Status: {config.text}
      </Text>
    </Space>
  );
};

export default ConnectionStatus;

import React, { useState } from 'react';
import { Tree, Tag, Typography, Space, Divider } from 'antd';
import { FileOutlined, FolderOutlined, LockOutlined, StarOutlined } from '@ant-design/icons';
import PropertyDetailsCard from './PropertyDetailsCard';

const { Title } = Typography;

const ObjectModelVisualizer = ({ models }) => {
  const [selectedProperty, setSelectedProperty] = useState(null);

  const getTypeColor = (type) => {
    const colorMap = {
      'String': 'blue',
      'int': 'green',
      'Integer': 'green',
      'double': 'orange',
      'Double': 'orange',
      'boolean': 'purple',
      'Boolean': 'purple',
      'LocalDateTime': 'cyan',
      'List': 'magenta'
    };
    return colorMap[type] || 'default';
  };

  const buildTreeData = (models) => {
    return models.map(model => ({
      title: (
        <Space>
          <FolderOutlined style={{ color: '#1890ff' }} />
          <strong>{model.className}</strong>
          <Tag color="blue">Class</Tag>
        </Space>
      ),
      key: model.className,
      children: buildPropertyNodes(model.properties, model.className)
    }));
  };

  const buildPropertyNodes = (properties, parentKey = '') => {
    return properties?.map(prop => ({
      title: (
        <Space 
          style={{ cursor: 'pointer' }}
          onClick={() => setSelectedProperty(prop)}
        >
          <FileOutlined style={{ color: prop.sensitive ? '#ff7875' : '#52c41a' }} />
          <span className="property-name">{prop.name}</span>
          <Tag color={getTypeColor(prop.type)}>{prop.type}</Tag>
          {prop.required && <Tag color="red" icon={<StarOutlined />}>Required</Tag>}
          {prop.sensitive && <Tag color="orange" icon={<LockOutlined />}>Sensitive</Tag>}
        </Space>
      ),
      key: `${parentKey}.${prop.name}`,
      children: prop.nested && prop.nestedProperties ? 
                 buildPropertyNodes(prop.nestedProperties, `${parentKey}.${prop.name}`) : 
                 undefined
    })) || [];
  };

  if (!models || models.length === 0) {
    return (
      <div style={{ textAlign: 'center', padding: '48px' }}>
        <Typography.Text type="secondary">
          No object models available
        </Typography.Text>
      </div>
    );
  }

  return (
    <div style={{ display: 'flex', gap: '24px' }}>
      <div style={{ flex: 1 }}>
        <Title level={4}>Object Structure</Title>
        <Tree
          treeData={buildTreeData(models)}
          defaultExpandAll
          showIcon={false}
          style={{ background: '#fafafa', padding: '16px', borderRadius: '6px' }}
        />
      </div>
      
      {selectedProperty && (
        <div style={{ flex: 1 }}>
          <Divider orientation="left">Property Details</Divider>
          <PropertyDetailsCard property={selectedProperty} />
        </div>
      )}
    </div>
  );
};

export default ObjectModelVisualizer;



import React from 'react';
import { Card, Tag, Typography, Space, Descriptions } from 'antd';
import { LockOutlined, StarOutlined, InfoCircleOutlined } from '@ant-design/icons';

const { Text, Paragraph } = Typography;

const PropertyDetailsCard = ({ property }) => {
  const getTypeColor = (type) => {
    const colorMap = {
      'String': 'blue',
      'int': 'green',
      'Integer': 'green',
      'double': 'orange',
      'Double': 'orange',
      'boolean': 'purple',
      'Boolean': 'purple',
      'LocalDateTime': 'cyan',
      'List': 'magenta'
    };
    return colorMap[type] || 'default';
  };

  return (
    <Card 
      title={
        <Space>
          <InfoCircleOutlined />
          <Text strong>{property.name}</Text>
        </Space>
      }
      size="small"
    >
      <Descriptions column={1} size="small">
        <Descriptions.Item label="Type">
          <Tag color={getTypeColor(property.type)}>{property.type}</Tag>
        </Descriptions.Item>
        
        <Descriptions.Item label="Description">
          <Paragraph style={{ margin: 0 }}>
            {property.description}
          </Paragraph>
        </Descriptions.Item>
        
        <Descriptions.Item label="Attributes">
          <Space>
            {property.required && (
              <Tag color="red" icon={<StarOutlined />}>
                Required
              </Tag>
            )}
            {property.sensitive && (
              <Tag color="orange" icon={<LockOutlined />}>
                Sensitive
              </Tag>
            )}
            {property.nested && (
              <Tag color="purple">
                Complex Object
              </Tag>
            )}
            {property.category && (
              <Tag color="blue">
                {property.category}
              </Tag>
            )}
          </Space>
        </Descriptions.Item>
        
        {property.example && (
          <Descriptions.Item label="Example">
            <Text code>{property.example}</Text>
          </Descriptions.Item>
        )}
      </Descriptions>
      
      {property.nestedProperties && property.nestedProperties.length > 0 && (
        <div style={{ marginTop: '16px' }}>
          <Text strong>Nested Properties:</Text>
          <div style={{ marginTop: '8px' }}>
            {property.nestedProperties.map((nested, index) => (
              <Tag key={index} style={{ margin: '2px' }}>
                {nested.name} ({nested.type})
              </Tag>
            ))}
          </div>
        </div>
      )}
    </Card>
  );
};

export default PropertyDetailsCard;



import React from 'react';
import { Descriptions, Table, Tag, Typography, Space } from 'antd';
import { ShoppingCartOutlined, UserOutlined, CalendarOutlined } from '@ant-design/icons';

const { Title, Text } = Typography;

const OrderDisplay = ({ order }) => {
  const getStatusColor = (status) => {
    const colors = {
      PENDING: 'orange',
      PROCESSING: 'blue',
      SHIPPED: 'cyan',
      DELIVERED: 'green',
      CANCELLED: 'red'
    };
    return colors[status] || 'default';
  };

  const formatDate = (dateString) => {
    return new Date(dateString).toLocaleString();
  };

  const productColumns = [
    {
      title: 'Product ID',
      dataIndex: 'productId',
      key: 'productId',
      render: (text) => <Text code>{text}</Text>
    },
    {
      title: 'Name',
      dataIndex: 'productName',
      key: 'productName',
    },
    {
      title: 'Category',
      dataIndex: 'category',
      key: 'category',
      render: (category) => <Tag color="blue">{category}</Tag>
    },
    {
      title: 'Price',
      dataIndex: 'price',
      key: 'price',
      render: (price) => price ? `$${price.toFixed(2)}` : 'N/A'
    },
    {
      title: 'Quantity',
      dataIndex: 'quantity',
      key: 'quantity',
    },
    {
      title: 'Subtotal',
      key: 'subtotal',
      render: (_, record) => {
        if (record.price && record.quantity) {
          return <Text strong>${(record.price * record.quantity).toFixed(2)}</Text>;
        }
        return 'N/A';
      }
    }
  ];

  return (
    <div>
      <Space direction="vertical" size="large" style={{ width: '100%' }}>
        <div>
          <Title level={4}>
            <ShoppingCartOutlined /> Order Details
          </Title>
          
          <Descriptions bordered size="small" column={2}>
            <Descriptions.Item label="Order ID" span={1}>
              <Text code>{order.orderId}</Text>
            </Descriptions.Item>
            
            <Descriptions.Item label="Status" span={1}>
              <Tag color={getStatusColor(order.status)}>
                {order.status}
              </Tag>
            </Descriptions.Item>
            
            {order.customerName && (
              <Descriptions.Item label={<><UserOutlined /> Customer</>} span={1}>
                {order.customerName}
              </Descriptions.Item>
            )}
            
            {order.orderDate && (
              <Descriptions.Item label={<><CalendarOutlined /> Order Date</>} span={1}>
                {formatDate(order.orderDate)}
              </Descriptions.Item>
            )}
            
            {order.totalAmount && (
              <Descriptions.Item label="Total Amount" span={2}>
                <Text strong style={{ fontSize: '16px', color: '#1890ff' }}>
                  ${order.totalAmount.toFixed(2)}
                </Text>
              </Descriptions.Item>
            )}
          </Descriptions>
        </div>
        
        {order.products && order.products.length > 0 && (
          <div>
            <Title level={5}>Products ({order.products.length})</Title>
            <Table
              dataSource={order.products}
              columns={productColumns}
              rowKey="productId"
              size="small"
              pagination={false}
              style={{ background: 'white' }}
            />
          </div>
        )}
      </Space>
    </div>
  );
};

export default OrderDisplay;



<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta name="description" content="OrderModel ABAC Portal - Real-time Java Object Model Visualization" />
    <title>OrderModel ABAC Portal</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>




