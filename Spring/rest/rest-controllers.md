## Spring rest controller with mapping (dozer), api description(swagger), service injection and slf4j logging:

```java
package com.exanple;

import io.swagger.annotations.Api;
import io.swagger.annotations.SwaggerDefinition;
import io.swagger.annotations.Tag;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.ArraySchema;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import lombok.extern.slf4j.Slf4j;
import org.dozer.Mapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.validation.Validator;
import java.util.List;
import java.util.Map;
import java.util.Optional;
import java.util.Set;
import java.util.stream.Collectors;

@Slf4j
@Validated
@RestController
@RequestMapping(value = "/api", produces = MediaType.APPLICATION_JSON_VALUE)
@Api("API description")
@SwaggerDefinition(tags = {
        @Tag(name = SwaggerTags.EXTERNAL, description = "API description")})
public class RestResource {

    private final Mapper mapper;
    private final Service service;

    @Autowired
    public RestResource(Mapper mapper, Service service) {
        this.mapper = mapper;
        this.service = service;
    }

    @GetMapping
    @Operation(summary = "Call description", tags = {SwaggerTags.EXTERNAL})
    @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Response contains a collection of response dto", content = @Content(array = @ArraySchema(schema = @Schema(implementation = ResponseDto.class)))),

            @ApiResponse(responseCode = "401", description = "Unauthorized"),

            @ApiResponse(responseCode = "403", description = "Forbidden"),

            @ApiResponse(responseCode = "404", description = "Not Found")})
    public List<ResponseDto> findAll(
            @RequestParam(required = false) Long param1,
            @RequestParam String tenantId,
            @RequestParam(required = false) String param2,
            @RequestParam(required = false) String param3,
            @RequestParam(required = false) String param4,
            @RequestParam(required = false) String param5,
            @RequestParam(required = false) Long param6
    ) {
        Model.ModelBuilder builder = Model.builder()
                .param1(param1)
                .param2(param2)
                .param3(param3)
                .param4(param4)
                .param5(param5)
                .param6(param6);
        
        var model = builder.build();

        return service.findByExample(model).stream()
                .map(c -> mapper.map(c, ResponseDto.class))
                .collect(Collectors.toList());
    }

    @PostMapping
    @Operation(summary = "Call description", tags = {SwaggerTags.EXTERNAL})
    @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Response contains a response dto", content = @Content(schema = @Schema(implementation = ResponseDto.class))),

            @ApiResponse(responseCode = "401", description = "Unauthorized"),

            @ApiResponse(responseCode = "403", description = "Forbidden"),

            @ApiResponse(responseCode = "404", description = "Not Found")})
    public ResponseEntity<ResponseDto> save(
            @RequestParam String paramId,
            @RequestBody(required = false) Optional<RequestDto> requestDto
    ) {

        var saveRequest = Model.builder()
                .paramId(paramId)
                .body(mapper.map(requestDto, ModelBody.class))
                .build();


        ResponseDto response = mapper.map(service.save(saveRequest), ResponseDto.class);
                
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }

    @PatchMapping
    @Operation(summary = "Call description", tags = {SwaggerTags.EXTERNAL})
    @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Successfully updated response dto", content = @Content(schema = @Schema(implementation = ResponseDto.class))),

            @ApiResponse(responseCode = "401", description = "Unauthorized"),

            @ApiResponse(responseCode = "403", description = "Forbidden"),

            @ApiResponse(responseCode = "404", description = "Not Found"),

            @ApiResponse(responseCode = "500", description = "Internal Server Error")})
    public ResponseEntity<ResponseDto> update(@RequestBody Map<String, Object> bodyMap) {
        ModelUpdateRequest modelUpdateRequest = mapAndValidate(bodyMap);

        ResponseDto response = mapper.map(
                service.update(
                        mapper.map(modelUpdateRequest, Model.class)
                ),
                ResponseDto.class
        );
        
        return ResponseEntity.status(HttpStatus.OK).body(response);
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "Call description", tags = {SwaggerTags.EXTERNAL})
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Model was successfully removed"),

            @ApiResponse(responseCode = "401", description = "Unauthorized"),

            @ApiResponse(responseCode = "403", description = "Forbidden"),

            @ApiResponse(responseCode = "404", description = "Not Found"),

            @ApiResponse(responseCode = "500", description = "Internal Server Error")})
    public void remove(@PathVariable Long id) {
        service.disableById(id);
    }
}

```

## Spring rest controller integration test:

```java
package com.exanple;

import org.dozer.Mapper;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.context.annotation.Import;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockHttpServletResponse;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.RequestBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.testcontainers.shaded.com.fasterxml.jackson.databind.ObjectMapper;

import java.util.List;
import java.util.Set;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

@WebMvcTest
@ContextConfiguration(classes = RestResource.class)
@AutoConfigureMockMvc(addFilters = false)
class RestResourceITest {

    @MockBean
    private Service service;

    @MockBean
    private Mapper mapper;

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private RestResource controller;

    @Test
    void findAllShouldReturnListOfExpectedData() throws Exception {
        String getRootEntity = new ObjectMapper().writeValueAsString(
                Request.builder()
                        .data(Set.of("data1", "data2"))
                        .build());
        String expectedJSONContent = new ObjectMapper().writeValueAsString(List.of(data));

        Mockito.when(service.findAll(Mockito.any(), Mockito.any())).thenReturn(List.of(Model));
        Mockito.when(mapper.map(Mockito.any(), Mockito.eq(Model.class))).thenReturn(ResponseDto);

        RequestBuilder requestBuilder =
                MockMvcRequestBuilders
                        .post("/api/...")
                        .contentType(MediaType.APPLICATION_JSON_VALUE)
                        .content(requestBody);
        
        MockHttpServletResponse response = mockMvc.perform(requestBuilder).andReturn().getResponse();

        Assertions.assertEquals(HttpStatus.OK.value(), response.getStatus());
        Assertions.assertEquals(MediaType.APPLICATION_JSON_VALUE, response.getContentType());
        Assertions.assertEquals(expectedJSONContent, response.getContentAsString());

        Mockito.verify(service).findAll(Mockito.any(), Mockito.any());
    }

    @Test
    void findAllShouldReturnListOfExpectedData() throws Exception {
        String expectedJSONContent = new ObjectMapper().writeValueAsString(List.of(data));

        Mockito.when(service.findByExample(Mockito.any())).thenReturn(List.of(Model));
        Mockito.when(mapper.map(Mockito.any(), Mockito.eq(ResponseDto.class))).thenReturn(ResponseDto);

        RequestBuilder requestBuilder = MockMvcRequestBuilders.get("/api/...");
        MockHttpServletResponse response = mockMvc.perform(requestBuilder).andReturn().getResponse();

        Assertions.assertEquals(HttpStatus.OK.value(), response.getStatus());
        Assertions.assertEquals(MediaType.APPLICATION_JSON_VALUE, response.getContentType());
        Assertions.assertEquals(expectedJSONContent, response.getContentAsString());

        Mockito.verify(service).findByExample(Mockito.any());
    }

    @Test
    void updateShouldReturnUpdatedObject() throws Exception {
        String expectedJSONContent = new ObjectMapper().writeValueAsString(Model);

        Mockito.when(service.update(Mockito.any())).thenReturn(Model);
        Mockito.when(mapper.map(Mockito.any(), Mockito.eq(ResponseDto.class))).thenReturn(ResponseDto);

        RequestBuilder requestBuilder = MockMvcRequestBuilders.patch("/api/...")
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .content(expectedJSONContent);
        MockHttpServletResponse response = mockMvc.perform(requestBuilder).andReturn().getResponse();

        Assertions.assertEquals(HttpStatus.OK.value(), response.getStatus());
        Assertions.assertEquals(MediaType.APPLICATION_JSON_VALUE, response.getContentType());
        Assertions.assertEquals(expectedJSONContent, response.getContentAsString());

        Mockito.verify(service).update(Mockito.any());
    }

    @Test
    void removeDisableServiceModelShouldBeCalled() throws Exception {
        RequestBuilder requestBuilder = MockMvcRequestBuilders.delete("/api.../" + ID_PATH_PARAMETER);
        MockHttpServletResponse response = mockMvc.perform(requestBuilder).andReturn().getResponse();

        Assertions.assertEquals(HttpStatus.OK.value(), response.getStatus());
        Mockito.verify(service).disableById(Mockito.any());
    }
}

```

## Articles
[Spring rest baeldung articles](https://www.baeldung.com/rest-with-spring-series)
[Spring testing documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html)
