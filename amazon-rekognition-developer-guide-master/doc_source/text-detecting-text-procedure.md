# Detecting text in an image<a name="text-detecting-text-procedure"></a>

You can provide an input image as an image byte array \(base64\-encoded image bytes\), or as an Amazon S3 object\. In this procedure, you upload a JPEG or PNG image to your S3 bucket and specify the file name\. 

**To detect text in an image \(API\)**

1. If you haven't already, complete the following prerequisites\.

   1. Create or update an AWS Identity and Access Management \(IAM\) user with `AmazonRekognitionFullAccess` and `AmazonS3ReadOnlyAccess` permissions\. For more information, see [Step 1: Set up an AWS account and create an IAM user](setting-up.md#setting-up-iam)\.

   1. Install and configure the AWS Command Line Interface and the AWS SDKs\. For more information, see [Step 2: Set up the AWS CLI and AWS SDKs](setup-awscli-sdk.md)\.

1. Upload the image that contains text to your S3 bucket\. 

   For instructions, see [Uploading Objects into Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/UploadingObjectsintoAmazonS3.html) in the *Amazon Simple Storage Service User Guide*\.

1. Use the following examples to call the `DetectText` operation\.

------
#### [ Java ]

   The following example code displays lines and words that were detected in an image\. 

   Replace the values of `bucket` and `photo` with the names of the S3 bucket and image that you used in step 2\. 

   ```
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   package aws.example.rekognition.image;
   import com.amazonaws.services.rekognition.AmazonRekognition;
   import com.amazonaws.services.rekognition.AmazonRekognitionClientBuilder;
   import com.amazonaws.services.rekognition.model.AmazonRekognitionException;
   import com.amazonaws.services.rekognition.model.Image;
   import com.amazonaws.services.rekognition.model.S3Object;
   import com.amazonaws.services.rekognition.model.DetectTextRequest;
   import com.amazonaws.services.rekognition.model.DetectTextResult;
   import com.amazonaws.services.rekognition.model.TextDetection;
   import java.util.List;
   
   
   
   public class DetectText {
   
      public static void main(String[] args) throws Exception {
         
     
         String photo = "inputtext.jpg";
         String bucket = "bucket";
   
         AmazonRekognition rekognitionClient = AmazonRekognitionClientBuilder.defaultClient();
   
        
         
         DetectTextRequest request = new DetectTextRequest()
                 .withImage(new Image()
                 .withS3Object(new S3Object()
                 .withName(photo)
                 .withBucket(bucket)));
       
   
         try {
            DetectTextResult result = rekognitionClient.detectText(request);
            List<TextDetection> textDetections = result.getTextDetections();
   
            System.out.println("Detected lines and words for " + photo);
            for (TextDetection text: textDetections) {
         
                    System.out.println("Detected: " + text.getDetectedText());
                    System.out.println("Confidence: " + text.getConfidence().toString());
                    System.out.println("Id : " + text.getId());
                    System.out.println("Parent Id: " + text.getParentId());
                    System.out.println("Type: " + text.getType());
                    System.out.println();
            }
         } catch(AmazonRekognitionException e) {
            e.printStackTrace();
         }
      }
   }
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javav2/example_code/rekognition/src/main/java/com/example/rekognition/DetectText.java)\.

   ```
       public static void detectTextLabels(RekognitionClient rekClient, String sourceImage) {
   
           try {
               InputStream sourceStream = new FileInputStream(sourceImage);
               SdkBytes sourceBytes = SdkBytes.fromInputStream(sourceStream);
               Image souImage = Image.builder()
                   .bytes(sourceBytes)
                   .build();
   
               DetectTextRequest textRequest = DetectTextRequest.builder()
                   .image(souImage)
                   .build();
   
               DetectTextResponse textResponse = rekClient.detectText(textRequest);
               List<TextDetection> textCollection = textResponse.textDetections();
               System.out.println("Detected lines and words");
               for (TextDetection text: textCollection) {
                   System.out.println("Detected: " + text.detectedText());
                   System.out.println("Confidence: " + text.confidence().toString());
                   System.out.println("Id : " + text.id());
                   System.out.println("Parent Id: " + text.parentId());
                   System.out.println("Type: " + text.type());
                   System.out.println();
               }
   
           } catch (RekognitionException | FileNotFoundException e) {
               System.out.println(e.getMessage());
               System.exit(1);
           }
       }
   ```

------
#### [ AWS CLI ]

   This AWS CLI command displays the JSON output for the `detect-text` CLI operation\. 

   Replace the values of `Bucket` and `Name` with the names of the S3 bucket and image that you used in step 2\. 

   ```
   aws rekognition detect-text \
   --image "S3Object={Bucket=bucketname,Name=input.jpg}"
   ```

------
#### [ Python ]

   The following example code displays lines and words detected in an image\. 

   Replace the values of `bucket` and `photo` with the names of the S3bucket and image that you used in step 2\. 

   ```
   #Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   import boto3
   
   def detect_text(photo, bucket):
   
       client=boto3.client('rekognition')
   
       response=client.detect_text(Image={'S3Object':{'Bucket':bucket,'Name':photo}})
                           
       textDetections=response['TextDetections']
       print ('Detected text\n----------')
       for text in textDetections:
               print ('Detected text:' + text['DetectedText'])
               print ('Confidence: ' + "{:.2f}".format(text['Confidence']) + "%")
               print ('Id: {}'.format(text['Id']))
               if 'ParentId' in text:
                   print ('Parent Id: {}'.format(text['ParentId']))
               print ('Type:' + text['Type'])
               print()
       return len(textDetections)
   
   def main():
   
       bucket='bucket'
       photo='photo'
       text_count=detect_text(photo,bucket)
       print("Text detected: " + str(text_count))
   
   
   if __name__ == "__main__":
       main()
   ```

------
#### [ \.NET ]

   The following example code displays lines and words detected in an image\. 

   Replace the values of `bucket` and `photo` with the names of the S3 bucket and image that you used in step 2\. 

   ```
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   using System;
   using Amazon.Rekognition;
   using Amazon.Rekognition.Model;
   
   public class DetectText
   {
       public static void Example()
       {
           String photo = "input.jpg";
           String bucket = "bucket";
   
           AmazonRekognitionClient rekognitionClient = new AmazonRekognitionClient();
   
           DetectTextRequest detectTextRequest = new DetectTextRequest()
           {
               Image = new Image()
               {
                   S3Object = new S3Object()
                   {
                       Name = photo,
                       Bucket = bucket
                   }
               }
           };
   
           try
           {
               DetectTextResponse detectTextResponse = rekognitionClient.DetectText(detectTextRequest);
               Console.WriteLine("Detected lines and words for " + photo);
               foreach (TextDetection text in detectTextResponse.TextDetections)
               {
                   Console.WriteLine("Detected: " + text.DetectedText);
                   Console.WriteLine("Confidence: " + text.Confidence);
                   Console.WriteLine("Id : " + text.Id);
                   Console.WriteLine("Parent Id: " + text.ParentId);
                   Console.WriteLine("Type: " + text.Type);
               }
           }
           catch (Exception e)
           {
               Console.WriteLine(e.Message);
           }
       }
   }
   ```

------
#### [ Node\.JS ]

   The following example code displays lines and words detected in an image\. 

   Replace the values of `bucket` and `photo` with the names of the S3bucket and image that you used in step 2\. Replace the value of `region` with the region found in your \.aws credentials\.

   ```
   var AWS = require('aws-sdk');
   
   const bucket = 'bucket' // the bucketname without s3://
   const photo  = 'photo' // the name of file
   
   const config = new AWS.Config({
     accessKeyId: process.env.AWS_ACCESS_KEY_ID,
     secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
   }) 
   AWS.config.update({region:'region'});
   const client = new AWS.Rekognition();
   const params = {
     Image: {
       S3Object: {
         Bucket: bucket,
         Name: photo
       },
     },
   }
   client.detectText(params, function(err, response) {
     if (err) {
       console.log(err, err.stack); // handle error if an error occurred
     } else {
       console.log(`Detected Text for: ${photo}`)
       console.log(response)
       response.TextDetections.forEach(label => {
         console.log(`Detected Text: ${label.DetectedText}`),
         console.log(`Type: ${label.Type}`),
         console.log(`ID: ${label.Id}`),
         console.log(`Parent ID: ${label.ParentId}`),
         console.log(`Confidence: ${label.Confidence}`),
         console.log(`Polygon: `)
         console.log(label.Geometry.Polygon)
       } 
       )
     } 
   });
   ```

------

## DetectText operation request<a name="detecttext-request"></a>

In the `DetectText` operation, you supply an input image either as a base64\-encoded byte array or as an image stored in an Amazon S3 bucket\. The following example JSON request shows the image loaded from an Amazon S3 bucket\.

```
{
    "Image": {
        "S3Object": {
            "Bucket": "bucket",
            "Name": "inputtext.jpg"
        }
    }
}
```

### Filters<a name="text-filters"></a>

Filtering by text region, size and confidence score provides you with additional flexibility to control your text detection output\. By using regions of interest, you can easily limit text detection to the regions that are relevant to you, for example, the top right of profile photo or a fixed location in relation to a reference point when reading parts numbers from an image of a machine\. Word bounding box size filter can be used to avoid small background text which may be noisy or irrelevant\. And lastly, word confidence filter enables you to remove results that may be unreliable due to being blurry or smudged\. You can use the following filters:
+ **MinConfidence** –Sets the confidence level of word detection\. Words with detection confidence below this level are excluded from the result\. Values should be between 0 and 100\. The default MinConfidence is 0\.
+ **MinBoundingBoxWidth** – Sets the minimum width of the word bounding box\. Words with bounding boxes that are smaller than this value are excluded from the result\. The value is relative to the image frame width\.
+ **MinBoundingBoxHeight** – Sets the minimum height of the word bounding box\. Words with bounding box heights less than this value are excluded from the result\. The value is relative to the image frame height\.
+ **RegionsOfInterest** – Limits detection to a specific region of the image frame\. The values are relative to the frame's dimensions\. For text only partially within a region, the response is undefined\.

## DetectText operation response<a name="text-response"></a>

The `DetectText` operation analyzes the image and returns an array, TextDetections, where each element \(`[TextDetection](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_TextDetection.html)`\) represents a line or word detected in the image\. For each element, `DetectText` returns the following information: 
+ The detected text \(`DetectedText`\)
+ The relationships between words and lines \(`Id` and `ParentId`\)
+ The location of text on the image \(`Geometry`\)
+ The confidence Amazon Rekognition has in the accuracy of the detected text and bounding box \(`Confidence`\)
+ The type of the detected text \(`Type`\)

### Detected text<a name="text-detected-text"></a>

Each `TextDetection` element contains recognized text \(words or lines\) in the `DetectedText` field\. A word is one or more script characters not separated by spaces\. `DetectText` can detect up to 100 words in an image\. Returned text might include characters that make a word unrecognizable\. For example, *C@t* instead of *Cat*\. To determine whether a `TextDetection` element represents a line of text or a word, use the `Type` field\.

 

Each `TextDetection` element includes a percentage value that represents the degree of confidence that Amazon Rekognition has in the accuracy of the detected text and of the bounding box that surrounds the text\. 

### Word and line relationships<a name="text-ids"></a>

Each `TextDetection` element has an identifier field, `Id`\. The `Id` shows the word's position in a line\. If the element is a word, the parent identifier field, `ParentId`, identifies the line where the word was detected\. The `ParentId` for a line is null\. For example, the line "but keep" in the example image has the following the `Id` and `ParentId` values: 


|  Text  |  ID  |  Parent ID  | 
| --- | --- | --- | 
|  but keep  |  3  |     | 
|  but  |  8  |  3  | 
|  keep  |  9  |  3  | 

### Text location on an image<a name="text-location"></a>

To determine where the recognized text is on an image, use the bounding box \([Geometry](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_Geometry.html)\) information that's returned by `DetectText`\. The `Geometry` object contains two types of bounding box information for detected lines and words:
+ An axis\-aligned coarse rectangular outline in a [BoundingBox](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_BoundingBox.html) object
+ A finer\-grained polygon that's made up of multiple X and Y coordinates in a [Point](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_Point.html) array

The bounding box and polygon coordinates show where the text is located on the source image\. The coordinate values are a ratio of the overall image size\. For more information, see [BoundingBox](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_BoundingBox.html)\. 

The following JSON response from the `DetectText` operation shows the words and lines that were detected in the following image\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/rekognition/latest/dg/images/text.png)

```
{
    "TextDetections": [
        {
            "Confidence": 90.54900360107422,
            "DetectedText": "IT'S",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.10317354649305344,
                    "Left": 0.6677391529083252,
                    "Top": 0.17569075524806976,
                    "Width": 0.15113449096679688
                },
                "Polygon": [
                    {
                        "X": 0.6677391529083252,
                        "Y": 0.17569075524806976
                    },
                    {
                        "X": 0.8188736438751221,
                        "Y": 0.17574213445186615
                    },
                    {
                        "X": 0.8188582062721252,
                        "Y": 0.278915673494339
                    },
                    {
                        "X": 0.6677237153053284,
                        "Y": 0.2788642942905426
                    }
                ]
            },
            "Id": 0,
            "Type": "LINE"
        },
        {
            "Confidence": 59.411651611328125,
            "DetectedText": "I",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.05955825746059418,
                    "Left": 0.2763049304485321,
                    "Top": 0.394121915102005,
                    "Width": 0.026684552431106567
                },
                "Polygon": [
                    {
                        "X": 0.2763049304485321,
                        "Y": 0.394121915102005
                    },
                    {
                        "X": 0.30298948287963867,
                        "Y": 0.3932435214519501
                    },
                    {
                        "X": 0.30385109782218933,
                        "Y": 0.45280176401138306
                    },
                    {
                        "X": 0.27716654539108276,
                        "Y": 0.453680157661438
                    }
                ]
            },
            "Id": 1,
            "Type": "LINE"
        },
        {
            "Confidence": 92.76634979248047,
            "DetectedText": "MONDAY",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.11997425556182861,
                    "Left": 0.5545867085456848,
                    "Top": 0.34920141100883484,
                    "Width": 0.39841532707214355
                },
                "Polygon": [
                    {
                        "X": 0.5545867085456848,
                        "Y": 0.34920141100883484
                    },
                    {
                        "X": 0.9530020356178284,
                        "Y": 0.3471102714538574
                    },
                    {
                        "X": 0.9532787799835205,
                        "Y": 0.46708452701568604
                    },
                    {
                        "X": 0.554863452911377,
                        "Y": 0.46917566657066345
                    }
                ]
            },
            "Id": 2,
            "Type": "LINE"
        },
        {
            "Confidence": 96.7636489868164,
            "DetectedText": "but keep",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.0756164938211441,
                    "Left": 0.634815514087677,
                    "Top": 0.5181083083152771,
                    "Width": 0.20877975225448608
                },
                "Polygon": [
                    {
                        "X": 0.634815514087677,
                        "Y": 0.5181083083152771
                    },
                    {
                        "X": 0.8435952663421631,
                        "Y": 0.52589350938797
                    },
                    {
                        "X": 0.8423560857772827,
                        "Y": 0.6015099883079529
                    },
                    {
                        "X": 0.6335763335227966,
                        "Y": 0.59372478723526
                    }
                ]
            },
            "Id": 3,
            "Type": "LINE"
        },
        {
            "Confidence": 99.47185516357422,
            "DetectedText": "Smiling",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.2814019024372101,
                    "Left": 0.48475268483161926,
                    "Top": 0.6823741793632507,
                    "Width": 0.47539761662483215
                },
                "Polygon": [
                    {
                        "X": 0.48475268483161926,
                        "Y": 0.6823741793632507
                    },
                    {
                        "X": 0.9601503014564514,
                        "Y": 0.587857186794281
                    },
                    {
                        "X": 0.9847385287284851,
                        "Y": 0.8692590594291687
                    },
                    {
                        "X": 0.5093409419059753,
                        "Y": 0.9637760519981384
                    }
                ]
            },
            "Id": 4,
            "Type": "LINE"
        },
        {
            "Confidence": 90.54900360107422,
            "DetectedText": "IT'S",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.10387301445007324,
                    "Left": 0.6685508489608765,
                    "Top": 0.17597118020057678,
                    "Width": 0.14985692501068115
                },
                "Polygon": [
                    {
                        "X": 0.6677391529083252,
                        "Y": 0.17569075524806976
                    },
                    {
                        "X": 0.8188736438751221,
                        "Y": 0.17574213445186615
                    },
                    {
                        "X": 0.8188582062721252,
                        "Y": 0.278915673494339
                    },
                    {
                        "X": 0.6677237153053284,
                        "Y": 0.2788642942905426
                    }
                ]
            },
            "Id": 5,
            "ParentId": 0,
            "Type": "WORD"
        },
        {
            "Confidence": 92.76634979248047,
            "DetectedText": "MONDAY",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.11929994821548462,
                    "Left": 0.5540683269500732,
                    "Top": 0.34858056902885437,
                    "Width": 0.3998897075653076
                },
                "Polygon": [
                    {
                        "X": 0.5545867085456848,
                        "Y": 0.34920141100883484
                    },
                    {
                        "X": 0.9530020356178284,
                        "Y": 0.3471102714538574
                    },
                    {
                        "X": 0.9532787799835205,
                        "Y": 0.46708452701568604
                    },
                    {
                        "X": 0.554863452911377,
                        "Y": 0.46917566657066345
                    }
                ]
            },
            "Id": 7,
            "ParentId": 2,
            "Type": "WORD"
        },
        {
            "Confidence": 59.411651611328125,
            "DetectedText": "I",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.05981886386871338,
                    "Left": 0.2779299318790436,
                    "Top": 0.3935416042804718,
                    "Width": 0.02624112367630005
                },
                "Polygon": [
                    {
                        "X": 0.2763049304485321,
                        "Y": 0.394121915102005
                    },
                    {
                        "X": 0.30298948287963867,
                        "Y": 0.3932435214519501
                    },
                    {
                        "X": 0.30385109782218933,
                        "Y": 0.45280176401138306
                    },
                    {
                        "X": 0.27716654539108276,
                        "Y": 0.453680157661438
                    }
                ]
            },
            "Id": 6,
            "ParentId": 1,
            "Type": "WORD"
        },
        {
            "Confidence": 95.33189392089844,
            "DetectedText": "but",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.06849122047424316,
                    "Left": 0.6350157260894775,
                    "Top": 0.5214487314224243,
                    "Width": 0.08413040637969971
                },
                "Polygon": [
                    {
                        "X": 0.6347596645355225,
                        "Y": 0.5215170383453369
                    },
                    {
                        "X": 0.719483494758606,
                        "Y": 0.5212655067443848
                    },
                    {
                        "X": 0.7195737957954407,
                        "Y": 0.5904868841171265
                    },
                    {
                        "X": 0.6348499655723572,
                        "Y": 0.5907384157180786
                    }
                ]
            },
            "Id": 8,
            "ParentId": 3,
            "Type": "WORD"
        },
        {
            "Confidence": 98.1954116821289,
            "DetectedText": "keep",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.07207882404327393,
                    "Left": 0.7295929789543152,
                    "Top": 0.5265749096870422,
                    "Width": 0.11196041107177734
                },
                "Polygon": [
                    {
                        "X": 0.7290706038475037,
                        "Y": 0.5251666903495789
                    },
                    {
                        "X": 0.842876672744751,
                        "Y": 0.5268880724906921
                    },
                    {
                        "X": 0.8423973917961121,
                        "Y": 0.5989891886711121
                    },
                    {
                        "X": 0.7285913228988647,
                        "Y": 0.5972678065299988
                    }
                ]
            },
            "Id": 9,
            "ParentId": 3,
            "Type": "WORD"
        },
        {
            "Confidence": 99.47185516357422,
            "DetectedText": "Smiling",
            "Geometry": {
                "BoundingBox": {
                    "Height": 0.3739858865737915,
                    "Left": 0.48920923471450806,
                    "Top": 0.5900818109512329,
                    "Width": 0.5097314119338989
                },
                "Polygon": [
                    {
                        "X": 0.48475268483161926,
                        "Y": 0.6823741793632507
                    },
                    {
                        "X": 0.9601503014564514,
                        "Y": 0.587857186794281
                    },
                    {
                        "X": 0.9847385287284851,
                        "Y": 0.8692590594291687
                    },
                    {
                        "X": 0.5093409419059753,
                        "Y": 0.9637760519981384
                    }
                ]
            },
            "Id": 10,
            "ParentId": 4,
            "Type": "WORD"
        }
    ]
}
```