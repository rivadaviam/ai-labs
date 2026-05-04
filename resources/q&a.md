Q1:
A company is developing an AI assistant that processes customer data by using Amazon Bedrock. The AI assistant has multiple guardrails. The guardrails include prompt injection detection, sensitive information filtering, and denied topic blocking.

When a customer query is blocked, a GenAI developer needs a detailed analysis of which specific guardrail rule was invoked and why the content was flagged. Then, the GenAI developer must fine-tune guardrail configurations and distinguish between legitimate customer queries and actual security threats.

Which configuration provides the MOST detailed analysis of guardrail decision-making for content filtering?

A1:
Configure guardrail tracing with ("trace":
"enabled", in guardrailConfig. Monitor Invocationsintervened metrics filtered by the GuardrailPolicyType dimensions:
ContentPolicy, TopicPolicy, and SensitivelnformationPolicy.

Q2:
A financial services company operates RAG for an application that answers user questions by using internal market analysis reports. The application uses Amazon Bedrock for the embedding model. The application uses an Amazon OpenSearch Service cluster as the vector store. An AWS Lambda function performs the embedding and search logic.

After a recent code update to the Lambda function, the application starts returning generic responses. For example, the application returns “no relevant information found” even for questions that previously returned accurate answers. Amazon CloudWatch Logs shows no errors. AWS X-Ray confirms successful FM invocation. The OpenSearch Service cluster is healthy. Query latency remains normal.

What is the cause of this issue?

A2:
The updated Lambda function uses a different version of the embedding model.

Q3:
A company is implementing AI governance policies. The policies require all FM interactions to be secured with guardrails. The company configures Amazon Bedrock guardrails. The company must ensure that all InvokeModel and Converse API calls to FMs apply the guardrails.

Which solution will enforce guardrail compliance for the API calls in the MOST operationally efficient way?

A3:
Configure IAM policies tor the InvokeModel nd Converse API calls with the
bedrock:Guardrailldentifier condition key.
Apply the policies to all IAM roles that access the Amazon Bedrock FMs.

Q4:
A company wants to create an application to analyze fashion trends. The application must analyze videos and photos from public fashion shows to understand style elements and trends. The solution must store the extracted information and provide a dashboard that summarizes the fashion trends.

Which solution will meet these requirements with the LEAST operational overhead?

A4:
Use AWS Step Functions to process videos and photos from fashion shows by using Amazon Bedrock multimodal FMs. Store analysis results in Amazon S3. Use an Amazon QuickSight dashboard to visualize trends in the data.

Amazon Bedrock supports multimodal FMs including Amazon Nova Pro and Claude Sonnet.
Multimodal FMs can directly analyze videos and photos from fashion shows. Multimodal FMs can extract style elements and fashion trend information without custom model development. Step Functions provides a managed way to coordinate the analysis workflow. You can use QuickSight to build and deploy dashboards without managing any servers or infrastructure. Therefore, this solution requires the least operational overhead.
Learn more about Step Functions and Amazon Bedrock.
Learn more about QuickSight.

Q6:
A company is building a diagnostic imaging application. The application needs to perform similarity searches across 50 million images to assist with diagnosing and treating patients. The application must process new images daily. The application will perform similarity searches infrequently when users need to find similar cases for reference. The company wants a cost-effective solution that provides responsive search performance without requiring infrastructure management.

Which solution will meet these requirements MOST cost-effectively?

A6:
Create an Amazon S3 vector bucket with vector indexes to store image embeddings and perform similarity searches.

S3 Vectors is a fully managed, serverless feature of Amazon S3 that provides scalable vector search capabilities. S3 Vectors can store and search vector data.
S3 Vectors can support up to billions of vectors. This solution is suitable for workloads with infrequent searches. You need to pay for only what you use. You do not need to provision infrastructure. Therefore, this solution is cost-effective for storing 50 million image vectors. S3 Vectors automatically optimizes vector data for low-cost performance as datasets scale.
Learn more about S3 Vectors for similarity searches.

Q7:
An ecommerce company has an application that uses Amazon Bedrock to generate product descriptions and recommendations. Currently, the application resides in a single AWS Region. When invoking a model in Amazon Bedrock during peak periods, the application receives an error. The error message says, "Too many requests, please wait before trying again."

The company must increase the throughput for invocations during peak periods without introducing additional operational overhead. The company must maintain compatibility with the existing Amazon Bedrock API. The company must use the same FM.

Which solution will meet these requirements in the MOST cost-effective way?

A7:
Use cross-Region inference to distribute traffic across multiple Regions within a geographic area.

Cross-Region inference automatically distributes traffic across multiple Regions within your geographic area to process your inference request.
Learn more about cross-Region inference.

Q11:
A company runs a question-answering application. The application uses an Amazon Bedrock knowledge base that ingests documents from multiple Amazon S3 buckets. The company needs to monitor the data ingestion process to identify and troubleshoot any issues with document processing.

Which solution will meet these requirements to monitor knowledge base operations?

A11:
Configure knowledge base logging with Amazon CloudWatch Logs as the destination.
Use CloudWatch Logs Insights to query for failed document processing.

Amazon Bedrock knowledge bases support a built-in logging system that you can configure to send logs to CloudWatch Logs. The logs track the status of files during data ingestion jobs. The jobs show whether the files were successfully ingested, ignored, or failed.
You can use CloudWatch Logs Insights to create queries that help identify specific issues. For example, you can query for documents that have the status RESOURCE_IGNORED, EMBEDDING_FAILED, or
INDEXING_FAILED.

Q12:
A company uses an AI assistant to answer customer questions based on internal company documents. The company wants to include new documents in the assistant's responses as soon as possible. The company wants to exclude deleted documents from the AI assistant's responses as soon as possible.

The documents are stored in Amazon S3. The AI assistant uses Amazon Bedrock Knowledge Bases. Amazon S3 is the data source of the vector store that the company uses for RAG. A GenAI developer must create a scalable, event-driven, and resilient solution.

Which solution will meet these requirements?

A12:
Configure S3 Event Notifications to send object-created and object-deleted events to an Amazon SQS queue. Create an AWS Lambda function to poll the queue and invoke IngestKnowledgeBaseDocuments for new objects and DeleteKnowledgeBaseDocuments for deleted objects.

This solution provides a scalable and resilient architecture to meet the requirements. You can use Amazon SQS as a buffer between S3 events and Lambda processing. The queue handles traffic spikes, provides automatic retries, and ensures that no updates are missed. This event-driven approach meets the requirements and maintains system reliability under varying loads.