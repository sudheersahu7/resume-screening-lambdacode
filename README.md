# resume-screening-lambdacode
import boto3
import zipfile
import io
import os
import re

s3_client = boto3.client('s3')

# Define the bucket and folder paths
BUCKET_NAME = 'resume-screening-sudheersahuu'
RESUMES_FOLDER = 'resumes/'
RESULTS_FOLDER = 'result/'

# Define the target skills, projects, and keywords
TARGET_KEYWORDS = ['Python', 'AWS', 'Lambda', 'DevOps', 'Machine Learning', 'kubernetes', 'ansible', 'bash scripting', 'terraforam', 'CI/CD', 'jenkins']

def lambda_handler(event, context):
    try:
        # List all resumes in the 'resumes' folder
        response = s3_client.list_objects_v2(Bucket=BUCKET_NAME, Prefix=RESUMES_FOLDER)
        if 'Contents' not in response:
            print('No resumes found in the resumes folder.')
            return {'statusCode': 200, 'body': 'No resumes to process.'}

        shortlisted_resumes = []

        for obj in response['Contents']:
            resume_key = obj['Key']
            if resume_key.endswith('.txt') or resume_key.endswith('.pdf'):
                # Read the resume content
                resume_content = s3_client.get_object(Bucket=BUCKET_NAME, Key=resume_key)['Body'].read()
                resume_text = resume_content.decode('utf-8', errors='ignore')

                # Check for target keywords
                if any(keyword.lower() in resume_text.lower() for keyword in TARGET_KEYWORDS):
                    # Add to shortlisted resumes
                    shortlisted_resumes.append(resume_key)
                    # Copy to 'result' folder
                    destination_key = RESULTS_FOLDER + os.path.basename(resume_key)
                    s3_client.copy_object(
                        Bucket=BUCKET_NAME,
                        CopySource={'Bucket': BUCKET_NAME, 'Key': resume_key},
                        Key=destination_key
                    )

        print(f'Shortlisted Resumes: {shortlisted_resumes}')
        return {'statusCode': 200, 'body': f'{len(shortlisted_resumes)} resumes processed and uploaded to results folder.'}

    except Exception as e:
        print(f'Error: {str(e)}')
        return {'statusCode': 500, 'body': str(e)}
