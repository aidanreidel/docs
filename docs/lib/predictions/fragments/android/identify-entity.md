The following APIs will enable you to identify entities (faces and/or celebrities) from images.

## Set up your backend

Run `amplify add predictions`, then use the following answers:

```console
? Please select from one of the categories below (Use arrow keys)
  `Identify`
? What would you like to identify? (Use arrow keys)
  `Identify Entities`
? Provide a friendly name for your resource
  `identifyEntities`
? Would you like use the default configuration? (Use arrow keys)
  `Default Configuration`
? Who should have access? (Use arrow keys)
  `Auth and Guest users`
```

Run `amplify push` to create the resources in the cloud.

## Working with the API

### Detect entities in an image

To detect general entities like facial features, landmarks etc, default configuration from CLI workflow will suffice (i.e. celebrity detection enabled & entity identification from collection disabled).

Amplify will now detect general entity features when `IdentifyActionType.DETECT_ENTITIES` is passed in. Results are mapped to `IdentifyEntitiesResult`. For example:

<amplify-block-switcher>
<amplify-block name="Java">

```java
public void detectEntities(Bitmap image) {
    Amplify.Predictions.identify(
            IdentifyActionType.DETECT_ENTITIES,
            image,
            result -> {
                IdentifyEntitiesResult identifyResult = (IdentifyEntitiesResult) result;
                EntityDetails metadata = identifyResult.getEntities().get(0);
                Log.i("MyAmplifyApp", metadata.getBox().toShortString());
            },
            error -> Log.e("MyAmplifyApp", "Entity detection failed", error)
    );
}
```

</amplify-block>
<amplify-block name="Kotlin">

```kotlin
fun detectEntities(image: Bitmap) {
    Amplify.Predictions.identify(
        IdentifyActionType.DETECT_ENTITIES,
        image,
        Consumer { result ->
            val identifyResult = result as IdentifyEntitiesResult
            val metadata = identifyResult.getEntities()[0]
            Log.i("MyAmplifyApp", metadata.getBox()!!.toShortString())
        },
        Consumer { error ->
            Log.e("MyAmplifyApp", "Entity detection failed", error)
        }
    )
}
```

</amplify-block>
<amplify-block name="RxJava">

```java
public void detectEntities(Bitmap image) {
    RxAmplify.Predictions.identify(IdentifyActionType.DETECT_ENTITIES, image)
            .subscribe(
                result -> {
                    IdentifyEntitiesResult identifyResult = (IdentifyEntitiesResult) result;
                    EntityDetails metadata = identifyResult.getEntities().get(0);
                    Log.i("MyAmplifyApp", metadata.getBox().toShortString());
                },
                error -> Log.e("MyAmplifyApp", "Entity detection failed", error)
            );
}
```

</amplify-block>
</amplify-block-switcher>

As a result of passing in an image, the bounding box ([`android.graphics.RectF`](https://developer.android.com/reference/android/graphics/RectF)) that captures detected entity will be printed to the console.

### Detect pre-determined entities in an image

You can also match entities from a pre-created [Amazon Rekognition Collection](https://docs.aws.amazon.com/rekognition/latest/dg/collections.html) in Amplify. To access this feature, you must used advanced configuration in Amplify CLI:

Run `amplify add predictions`, then use the following answers:

```console
? Please select from one of the categories below (Use arrow keys)
  `Identify`
? What would you like to identify? (Use arrow keys)
  `Identify Entities`
? Provide a friendly name for your resource
  `identifyEntities`
? Would you like use the default configuration? (Use arrow keys)
  `Advanced Configuration`
? Would you like to enable celebrity detection? (Y/n)
  `Y`
? Would you like to identify entities from a collection of images? (y/N)
  `Y`
? How many entities would you like to identify? (50)
  `10`
? Would you like to allow users to add images to this collection? (Use arrow keys)
  `Yes`
? Who should have access? (Use arrow keys)
  `Auth and Guest users`
```

Run `amplify push` to create the resources in the cloud

**Note**: If entity detection was already configured, run `amplify update predictions` to reconfigure as necessary.

The value of `collectionId` is the name of your collection, which can be created directly via CLI. The value of `maxEntities` must be a number greater than `0` or less than `51` (50 is the max number of entities Rekognition can detect from a collection). If either value of `collectionId` or `maxEntities` is invalid, then `identify` will just detect entities in general with facial features, landmarks, etc.

If both `collectionId` and `maxEntities` are properly configured, then Amplify will detect entity matches from the Rekognition Collection in your app. Results are mapped to `IdentifyEntityMatchesResult`. For example:

<amplify-block-switcher>
<amplify-block name="Java">

```java
public void detectEntities(Bitmap image) {
    Amplify.Predictions.identify(
            IdentifyActionType.DETECT_ENTITIES,
            image,
            result -> {
                IdentifyEntityMatchesResult identifyResult = (IdentifyEntityMatchesResult) result;
                EntityMatch match = identifyResult.getEntityMatches().get(0);
                Log.i("AmplifyQuickstart", match.getExternalImageId());
            },
            error -> Log.e("AmplifyQuickstart", "Identify failed", error)
    );
}
```

</amplify-block>
<amplify-block name="Kotlin">

```kotlin
fun detectEntities(image: Bitmap) {
    Amplify.Predictions.identify(
        IdentifyActionType.DETECT_ENTITIES,
        image,
        Consumer { result: IdentifyResult ->
            val identifyResult = result as IdentifyEntityMatchesResult
            val match = identifyResult.getEntityMatches()[0]
            Log.i("AmplifyQuickstart", match.externalImageId)
        },
        Consumer { error: PredictionsException ->
            Log.e("AmplifyQuickstart", "Identify failed", error)
        }
    )
}
```

</amplify-block>
<amplify-block name="RxJava">

```java
public void detectEntities(Bitmap image) {
    RxAmplify.Predictions.identify(IdentifyActionType.DETECT_ENTITIES, image)
            .subscribe(
                result -> {
                    IdentifyEntityMatchesResult identifyResult = (IdentifyEntityMatchesResult) result;
                    EntityMatch match = identifyResult.getEntityMatches().get(0);
                    Log.i("AmplifyQuickstart", match.getExternalImageId());
                },
                error -> Log.e("AmplifyQuickstart", "Identify failed", error)
            );
}
```

</amplify-block>
</amplify-block-switcher>

### Detecting Celebrities

To detect celebrities you can pass in `IdentifyActionType.DETECT_CELEBRITIES`.  Results are mapped to `IdentifyCelebritiesResult`.  For example:

<amplify-block-switcher>
<amplify-block name="Java">

```java
public void detectCelebs(Bitmap image) {
    Amplify.Predictions.identify(
            IdentifyActionType.DETECT_CELEBRITIES,
            image,
            result -> {
                IdentifyCelebritiesResult identifyResult = (IdentifyCelebritiesResult) result;
                CelebrityDetails metadata = identifyResult.getCelebrities().get(0);
                Log.i("MyAmplifyApp", metadata.getCelebrity().getName());
            },
            error -> Log.e("MyAmplifyApp", "Identify failed", error)
    );
}
```

</amplify-block>
<amplify-block name="Kotlin">

```kotlin
fun detectCelebs(image: Bitmap) {
    Amplify.Predictions.identify(
        IdentifyActionType.DETECT_CELEBRITIES,
        image,
        Consumer { result: IdentifyResult ->
            val identifyResult = result as IdentifyCelebritiesResult
            val metadata = identifyResult.getCelebrities()[0]
            Log.i("MyAmplifyApp", metadata.getCelebrity().name)
        },
        Consumer { error: PredictionsException ->
            Log.e("MyAmplifyApp", "Identify failed", error)
        }
    )
}
```

</amplify-block>
<amplify-block name="RxJava">

```java
public void detectCelebs(Bitmap image) {
    RxAmplify.Predictions.identify(IdentifyActionType.DETECT_CELEBRITIES, image)
            .subscribe(
                result -> {
                    IdentifyCelebritiesResult identifyResult = (IdentifyCelebritiesResult) result;
                    CelebrityDetails metadata = identifyResult.getCelebrities().get(0);
                    Log.i("MyAmplifyApp", metadata.getCelebrity().getName());
                },
                error -> Log.e("MyAmplifyApp", "Identify failed", error)
            );
}
```

</amplify-block>
</amplify-block-switcher>

As a result of passing in an image, `identify` will return the name of a detected celebrity.
