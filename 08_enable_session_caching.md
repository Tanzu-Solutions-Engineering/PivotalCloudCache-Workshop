## HTTP Session caching using PCC

Implement a session API for demonstrating http session offloading. This sample API creates a session object and increments no. of page hits.

#### Step 1: Create a region for storing session objects. Default region name is ClusteredSpringSessions

```
create region --name=ClusteredSpringSessions --type=PARTITION_HEAP_LRU
```

#### Step 2: Update the pom.xml to include the spring-session-data-gemfire dependency

```
<dependency>
	<groupId>org.springframework.session</groupId>
	<artifactId>spring-session-data-geode</artifactId>
</dependency>
```

#### Step 3: navigate to configuration file and enable session caching using @EnableGemFireHttpSession

```
...
@EnableGemFireHttpSession(poolName = "DEFAULT",regionName = "ClusteredSpringSessions")
@Configuration
public class CloudCacheConfig {
}
```

#### Step 4: Implement a controller for demonstrating session caching.

```
package io.pivotal.data.controller;

@RestController
public class HttpSessionController {


	private static final Logger LOGGER = LoggerFactory.getLogger(HttpSessionController.class);

	@RequestMapping(value = "/session")
    public String index(HttpSession httpSession) {

        Integer hits = (Integer) httpSession.getAttribute("hits");

        LOGGER.info("No. of Hits: '{}', session id '{}'", hits, httpSession.getId());

        if (hits == null) {
            hits = 0;
        }

        httpSession.setAttribute("hits", ++hits);
        return String.format("Session Id [<b>%1$s</b>] <br/>"
    			+ "No. of Hits [<b>%2$s</b>]%n", httpSession.getId(), httpSession.getAttribute("hits"));
    }

}
```

#### Step 5: Rebuild and push the app

#### Step 6: session API

http://pizza-store-pcc-client.apps.xyz.com/session

```
Session Id [0056EFC36B06C14619B3F14A4ED66272] 
No. of Hits [4]
```