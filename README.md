# IDX DMP Android SDK (pre alpha)

A simple sdk for calculating auditories

## Implementation

Add `implementation 'com.dxmdp.android:datamanagerprovider:0.1.0'` to `dependencies` section in `app/build.gradle`.

```
import com.dxmdp.android.DataManagerProvider;
import com.dxmdp.android.requests.event.EventRequestProperties;

...

public class MainActivity extends AppCompatActivity {
  ...

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    ...

    String providerId = "45c531d8-d959-4240-bb6b-3e7372326a58";
    dataManagerProvider = new DataManagerProvider(
        getApplicationContext(),
        providerId
    );

    EventRequestProperties eventRequestProperties = new EventRequestProperties();
    eventRequestProperties.url = "url";
    eventRequestProperties.title = "title";
    eventRequestProperties.domain = "domain";
    eventRequestProperties.author = "author";
    eventRequestProperties.category = "category";
    eventRequestProperties.description = "description";
    eventRequestProperties.tags = Arrays.asList(tags.split(","));

    dataManagerProvider.sendEventRequest(eventRequestProperties);
    
    ...
  }


  // AD request
  AdManagerAdRequest.Builder builder = new AdManagerAdRequest.Builder();
  AdManagerAdRequest adRequest = dataManagerProvider
      .addIDXAudiences(builder)
      .build();
```
