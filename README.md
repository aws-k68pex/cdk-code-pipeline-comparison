# AWS CDK: Pipeline Comparison

### CDK v1 (AWS CDK v1 has reached End-of-Support on 2023-06-01):
There are three different cdk construct for CodePipeline:
1. [@aws-cdk/aws-codepipeline](https://docs.aws.amazon.com/cdk/api/v1/docs/aws-codepipeline-readme.html) module:
Typescript: `@aws-cdk/aws-codepipeline`
  - class [Pipeline](https://docs.aws.amazon.com/cdk/api/v1/docs/@aws-cdk_aws-codepipeline.Pipeline.html) (construct)
**Description**: An AWS CodePipeline pipeline with its associated IAM role and S3 bucket.  

**Example**:
```
// create a pipeline
import * as codecommit from '@aws-cdk/aws-codecommit';

const pipeline = new codepipeline.Pipeline(this, 'Pipeline');

// add a stage
const sourceStage = pipeline.addStage({ stageName: 'Source' });

// add a source action to the stage
declare const repo: codecommit.Repository;
declare const sourceArtifact: codepipeline.Artifact;
sourceStage.addAction(new codepipeline_actions.CodeCommitSourceAction({
actionName: 'Source',
output: sourceArtifact,
repository: repo,
}));

// ... add more stages
```

2. [@aws-cdk/pipelines](https://docs.aws.amazon.com/cdk/api/v1/docs/pipelines-readme.html) module:
  - class [CdkPipeline](https://docs.aws.amazon.com/cdk/api/v1/docs/@aws-cdk_pipelines.CdkPipeline.html) (construct)
    + Typescript: `@aws-cdk/pipelines » CdkPipeline`
**Description**:
* Old API
* Defines an AWS CodePipeline-based Pipeline to deploy CDK applications
* Automatically manages the following:
  - Stack dependency order
  - Asset publishing
  - Keeping the pipeline up-to-date as the CDK apps change
  - Using stack outputs later on in the pipeline

**Example**:
```
// Original API
const cloudAssemblyArtifact = new codepipeline.Artifact();
const originalPipeline = new pipelines.CdkPipeline(this, 'Pipeline', {
  selfMutating: false,
  cloudAssemblyArtifact,
});
```
  - class [CodePipeline](https://docs.aws.amazon.com/cdk/api/v1/docs/@aws-cdk_pipelines.CodePipeline.html) (construct)
    + Typescript: `@aws-cdk/pipelines » CodePipeline`
**Description**:
- Modern API

**Example**:
```
// Modern API
const modernPipeline = new pipelines.CodePipeline(this, 'Pipeline', {
  selfMutation: false,
  synth: new pipelines.ShellStep('Synth', {
    input: pipelines.CodePipelineSource.connection('my-org/my-app', 'main', {
      connectionArn: 'arn:aws:codestar-connections:us-east-1:222222222222:connection/7d2469ff-514a-4e4f-9003-5ca4a43cdc41', // Created using the AWS console * });',
    }),
    commands: [
      'npm ci',
      'npm run build',
      'npx cdk synth',
    ],
  }),
});
```

## CDK v2:

1. class [Pipeline](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_codepipeline.Pipeline.html) (construct)
  - Typescript: `aws-cdk-lib » aws_codepipeline » Pipeline`  
**Description**: An AWS CodePipeline pipeline with its associated IAM role and S3 bucket.  
**Example**:  
```
// create a pipeline
import * as codecommit from 'aws-cdk-lib/aws-codecommit';

const pipeline = new codepipeline.Pipeline(this, 'Pipeline');

// add a stage
const sourceStage = pipeline.addStage({ stageName: 'Source' });

// add a source action to the stage
declare const repo: codecommit.Repository;
declare const sourceArtifact: codepipeline.Artifact;
sourceStage.addAction(new codepipeline_actions.CodeCommitSourceAction({
  actionName: 'Source',
  output: sourceArtifact,
  repository: repo,
}));

// ... add more stages
```

2. class [CodePipeline](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.pipelines.CodePipeline.html) (construct)
 - Typescript: `aws-cdk-lib » pipelines » CodePipeline`  
**Description**: This is a `Pipeline` with its `engine` property set to `CodePipelineEngine`, and exists for nicer ergonomics for users that don't need to switch out engines.  

**Example**:  
```
declare const codePipeline: codepipeline.Pipeline;

const sourceArtifact = new codepipeline.Artifact('MySourceArtifact');

const pipeline = new pipelines.CodePipeline(this, 'Pipeline', {
  codePipeline: codePipeline,
  synth: new pipelines.ShellStep('Synth', {
    input: pipelines.CodePipelineFileSet.fromArtifact(sourceArtifact),
    commands: ['npm ci','npm run build','npx cdk synth'],
  }),
});
```
