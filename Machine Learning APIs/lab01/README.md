# Lab 01 - Extract, Analyze, and Translate Text from Images with the Cloud ML API's


## Create API Key

Go to APIs & services > Credentials: Create Credentials > API Key 

```bash
export API_KEY=<YOUR_API_KEY>
```

## Upload an image to a cloud storage bucket
### Creating a Cloud Storage bucket

Storage > Create bucket > unique_name

### Upload an image to your bucket

Upload to the bucket the image ```resources/sign.png```

```bash
STR=$(jq .responses[0].textAnnotations[0].description ocr-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" translation-request.json
```

Next, click the checkbox under **"Share publicly"** This will make the image accessible to the Vision API from Cloud Shell

## Create your Vision API request

Create the file ```ocr-request.json``` with the content 

```json
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/sign.jpg"
          } 
        },
        "features": [
          {
            "type": "TEXT_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
```
