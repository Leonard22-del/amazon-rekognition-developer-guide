# Analyzing an image loaded from a local file system<a name="images-bytes"></a>

Amazon Rekognition Image operations can analyze images that are supplied as image bytes or images stored in an Amazon S3 bucket\.

These topics provide examples of supplying image bytes to Amazon Rekognition Image API operations by using a file loaded from a local file system\. You pass image bytes to an Amazon Rekognition API operation by using the [Image](https://docs.aws.amazon.com/rekognition/latest/APIReference/API_Image.html) input parameter\. Within `Image`, you specify the `Bytes` property to pass base64\-encoded image bytes\.

Image bytes passed to an Amazon Rekognition API operation by using the `Bytes` input parameter must be base64 encoded\. The AWS SDKs that these examples use automatically base64\-encode images\. You don't need to encode image bytes before calling an Amazon Rekognition API operation\. For more information, see [Image specifications](images-information.md)\. 

In this example JSON request for `DetectLabels`, the source image bytes are passed in the `Bytes` input parameter\. 

```
{
    "Image": {
        "Bytes": "/9j/4AAQSk....."
    },
    "MaxLabels": 10,
    "MinConfidence": 77
}
```

The following examples use various AWS SDKs and the AWS CLI to call `DetectLabels`\. For information about the `DetectLabels` operation response, see [DetectLabels response](labels-detect-labels-image.md#detectlabels-response)\.

For a client\-side JavaScript example, see [Using JavaScript](image-bytes-javascript.md)\.

**To detect labels in a local image**

1. If you haven't already:

   1. Create or update an IAM user with `AmazonRekognitionFullAccess` and `AmazonS3ReadOnlyAccess` permissions\. For more information, see [Step 1: Set up an AWS account and create an IAM user](setting-up.md#setting-up-iam)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 2: Set up the AWS CLI and AWS SDKs](setup-awscli-sdk.md)\.

1. Use the following examples to call the `DetectLabels` operation\.

------
#### [ Java ]

   The following Java example shows how to load an image from the local file system and detect labels by using the [detectLabels](https://docs.aws.amazon.com/sdk-for-java/latest/reference/com/amazonaws/services/rekognition/AmazonRekognition.html#detectLabels-com.amazonaws.services.rekognition.model.DetectLabelsRequest-) AWS SDK operation\. Change the value of `photo` to the path and file name of an image file \(\.jpg or \.png format\)\.

   ```
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   package aws.example.rekognition.image;
   import java.io.File;
   import java.io.FileInputStream;
   import java.io.InputStream;
   import java.nio.ByteBuffer;
   import java.util.List;
   import com.amazonaws.services.rekognition.AmazonRekognition;
   import com.amazonaws.services.rekognition.AmazonRekognitionClientBuilder;
   import com.amazonaws.AmazonClientException;
   import com.amazonaws.services.rekognition.model.AmazonRekognitionException;
   import com.amazonaws.services.rekognition.model.DetectLabelsRequest;
   import com.amazonaws.services.rekognition.model.DetectLabelsResult;
   import com.amazonaws.services.rekognition.model.Image;
   import com.amazonaws.services.rekognition.model.Label;
   import com.amazonaws.util.IOUtils;
   
   public class DetectLabelsLocalFile {
       public static void main(String[] args) throws Exception {
       	String photo="input.jpg";
   
   
           ByteBuffer imageBytes;
           try (InputStream inputStream = new FileInputStream(new File(photo))) {
               imageBytes = ByteBuffer.wrap(IOUtils.toByteArray(inputStream));
           }
   
   
           AmazonRekognition rekognitionClient = AmazonRekognitionClientBuilder.defaultClient();
   
           DetectLabelsRequest request = new DetectLabelsRequest()
                   .withImage(new Image()
                           .withBytes(imageBytes))
                   .withMaxLabels(10)
                   .withMinConfidence(77F);
   
           try {
   
               DetectLabelsResult result = rekognitionClient.detectLabels(request);
               List <Label> labels = result.getLabels();
   
               System.out.println("Detected labels for " + photo);
               for (Label label: labels) {
                  System.out.println(label.getName() + ": " + label.getConfidence().toString());
               }
   
           } catch (AmazonRekognitionException e) {
               e.printStackTrace();
           }
   
       }
   }
   ```

------
#### [ Python ]

   The following [AWS SDK for Python](https://aws.amazon.com/sdk-for-python/) example shows how to load an image from the local file system and call the [detect\_labels](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rekognition.html#Rekognition.Client.detect_labels) operation\. Change the value of `photo` to the path and file name of an image file \(\.jpg or \.png format\)\. 

   ```
   #Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   import boto3
   
   def detect_labels_local_file(photo):
   
   
       client=boto3.client('rekognition')
      
       with open(photo, 'rb') as image:
           response = client.detect_labels(Image={'Bytes': image.read()})
           
       print('Detected labels in ' + photo)    
       for label in response['Labels']:
           print (label['Name'] + ' : ' + str(label['Confidence']))
   
       return len(response['Labels'])
   
   def main():
       photo='photo'
   
       label_count=detect_labels_local_file(photo)
       print("Labels detected: " + str(label_count))
   
   
   if __name__ == "__main__":
       main()
   ```

------
#### [ \.NET ]

   The following example shows how to load an image from the local file system and detect labels by using the `DetectLabels` operation\. Change the value of `photo` to the path and file name of an image file \(\.jpg or \.png format\)\.

   ```
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
   using System;
   using System.IO;
   using Amazon.Rekognition;
   using Amazon.Rekognition.Model;
   
   public class DetectLabelsLocalfile
   {
       public static void Example()
       {
           String photo = "input.jpg";
   
           Amazon.Rekognition.Model.Image image = new Amazon.Rekognition.Model.Image();
           try
           {
               using (FileStream fs = new FileStream(photo, FileMode.Open, FileAccess.Read))
               {
                   byte[] data = null;
                   data = new byte[fs.Length];
                   fs.Read(data, 0, (int)fs.Length);
                   image.Bytes = new MemoryStream(data);
               }
           }
           catch (Exception)
           {
               Console.WriteLine("Failed to load file " + photo);
               return;
           }
   
           AmazonRekognitionClient rekognitionClient = new AmazonRekognitionClient();
   
           DetectLabelsRequest detectlabelsRequest = new DetectLabelsRequest()
           {
               Image = image,
               MaxLabels = 10,
               MinConfidence = 77F
           };
   
           try
           {
               DetectLabelsResponse detectLabelsResponse = rekognitionClient.DetectLabels(detectlabelsRequest);
               Console.WriteLine("Detected labels for " + photo);
               foreach (Label label in detectLabelsResponse.Labels)
                   Console.WriteLine("{0}: {1}", label.Name, label.Confidence);
           }
           catch (Exception e)
           {
               Console.WriteLine(e.Message);
           }
       }
   }
   ```

------
#### [ PHP ]

   The following [AWS SDK for PHP](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/welcome.html#getting-started) example shows how to load an image from the local file system and call the [DetectFaces](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-rekognition-2016-06-27.html#detectfaces) API operation\. Change the value of `photo` to the path and file name of an image file \(\.jpg or \.png format\)\. 

   ```
   
   <?php
   //Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
       require 'vendor/autoload.php';
   
       use Aws\Rekognition\RekognitionClient;
   
       $options = [
          'region'            => 'us-west-2',
           'version'           => 'latest'
       ];
   
       $rekognition = new RekognitionClient($options);
   	
       // Get local image
       $photo = 'input.jpg';
       $fp_image = fopen($photo, 'r');
       $image = fread($fp_image, filesize($photo));
       fclose($fp_image);
   
   
       // Call DetectFaces
       $result = $rekognition->DetectFaces(array(
          'Image' => array(
             'Bytes' => $image,
          ),
          'Attributes' => array('ALL')
          )
       );
   
       // Display info for each detected person
       print 'People: Image position and estimated age' . PHP_EOL;
       for ($n=0;$n<sizeof($result['FaceDetails']); $n++){
   
         print 'Position: ' . $result['FaceDetails'][$n]['BoundingBox']['Left'] . " "
         . $result['FaceDetails'][$n]['BoundingBox']['Top']
         . PHP_EOL
         . 'Age (low): '.$result['FaceDetails'][$n]['AgeRange']['Low']
         .  PHP_EOL
         . 'Age (high): ' . $result['FaceDetails'][$n]['AgeRange']['High']
         .  PHP_EOL . PHP_EOL;
       }
   ?>
   ```

------
#### [ Ruby ]

   This example displays a list of labels that were detected in the input image\. Change the value of `photo` to the path and file name of an image file \(\.jpg or \.png format\)\.

   ```
   #Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   #PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-rekognition-developer-guide/blob/master/LICENSE-SAMPLECODE.)
   
       # gem 'aws-sdk-rekognition'
       require 'aws-sdk-rekognition'
       credentials = Aws::Credentials.new(
          ENV['AWS_ACCESS_KEY_ID'],
          ENV['AWS_SECRET_ACCESS_KEY']
       )
       client   = Aws::Rekognition::Client.new credentials: credentials
       photo = 'photo.jpg'
       path = File.expand_path(photo) # expand path relative to the current directory
       file = File.read(path)
       attrs = {
         image: {
           bytes: file
         },
         max_labels: 10
       }
       response = client.detect_labels attrs
       puts "Detected labels for: #{photo}"
       response.labels.each do |label|
         puts "Label:      #{label.name}"
         puts "Confidence: #{label.confidence}"
         puts "Instances:"
         label['instances'].each do |instance|
           box = instance['bounding_box']
           puts "  Bounding box:"
           puts "    Top:        #{box.top}"
           puts "    Left:       #{box.left}"
           puts "    Width:      #{box.width}"
           puts "    Height:     #{box.height}"
           puts "  Confidence: #{instance.confidence}"
         end
         puts "Parents:"
         label.parents.each do |parent|
           puts "  #{parent.name}"
         end
         puts "------------"
         puts ""
       end
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javav2/example_code/rekognition/src/main/java/com/example/rekognition/DetectLabels.java)\.

   ```
       public static void detectImageLabels(RekognitionClient rekClient, String sourceImage) {
   
           try {
               InputStream sourceStream = new FileInputStream(sourceImage);
               SdkBytes sourceBytes = SdkBytes.fromInputStream(sourceStream);
   
               // Create an Image object for the source image.
               Image souImage = Image.builder()
                   .bytes(sourceBytes)
                   .build();
   
               DetectLabelsRequest detectLabelsRequest = DetectLabelsRequest.builder()
                   .image(souImage)
                   .maxLabels(10)
                   .build();
   
               DetectLabelsResponse labelsResponse = rekClient.detectLabels(detectLabelsRequest);
               List<Label> labels = labelsResponse.labels();
               System.out.println("Detected labels for the given photo");
               for (Label label: labels) {
                   System.out.println(label.name() + ": " + label.confidence().toString());
               }
   
           } catch (RekognitionException | FileNotFoundException e) {
               System.out.println(e.getMessage());
               System.exit(1);
           }
       }
   ```

------