In today’s hiring workflows, companies receive hundreds of resumes daily. Screening them manually is time-consuming. To solve this, I built a fully serverless resume screening system using:

AWS S3 — for resume storage
AWS Lambda — to process resumes automatically
Terraform — to provision infrastructure as code
⚡ The best part? It’s automated. Whenever a resume is uploaded, the system scans it in real-time and stores the result in another folder — no manual intervention needed!

🧱 Infrastructure — Defined with Terraform
Key Terraform Components:
S3 Bucket with folders:
resumes/: uploads go here
result/: processed results
spacy/: stores Lambda zip file
2. IAM Role & Policy for Lambda:

Grants S3 and CloudWatch access
3. Lambda Function:

Triggered by S3 uploads
Reads resume, applies logic, stores results
4. S3 Event Trigger:

Lambda is auto-invoked when a file lands in resumes/
