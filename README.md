# cloud_tips
clouds tips

import software.amazon.awssdk.auth.credentials.DefaultCredentialsProvider;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.ListObjectsV2Request;
import software.amazon.awssdk.services.s3.model.ListObjectsV2Response;
import software.amazon.awssdk.services.s3.model.S3Object;

public class ListS3BucketObjects {
    public static void main(String[] args) {
        // Replace with your AWS region and bucket name
        String region = "us-east-1";
        String bucketName = "your-bucket-name";

        S3Client s3Client = S3Client.builder()
                .region(Region.of(region))
                .credentialsProvider(DefaultCredentialsProvider.create())
                .build();

        ListObjectsV2Request listRequest = ListObjectsV2Request.builder()
                .bucket(bucketName)
                .maxKeys(1000) // Limit to 1000 objects per request
                .build();

        boolean moreObjectsToFetch = true;

        do {
            ListObjectsV2Response listResponse = s3Client.listObjectsV2(listRequest);

            for (S3Object s3Object : listResponse.contents()) {
                // Process each object as needed
                System.out.println("Object key: " + s3Object.key());
            }

            // If there are more objects to list, set the next continuation token
            if (listResponse.isTruncated()) {
                listRequest = listRequest.toBuilder()
                        .continuationToken(listResponse.nextContinuationToken())
                        .build();
            } else {
                moreObjectsToFetch = false;
            }
        } while (moreObjectsToFetch);

        s3Client.close();
    }
}
