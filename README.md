A full end-to-end kubernetes pipeline to showcase a full range of DevOps tools and principles.

# Deliverables

Provide a single archive of all challenge files.
Use the following layout:
## Part A
- k8s-manifests/
- manifests for Rocket Chat, GitLab
- webhook-app/
- all files pertaining to the GitLab webhook 🡪 Rocket Chat message application
- README.md


## Part B
- prometheus-grafana-values.yaml
- webhook-app-service-monitor.yaml
- webhook-app-dashboard.yaml
- prometheus-metrics-adaptor(-values).yaml
- README.md

Each part’s README should include:
Instructions on how to run each part of the challenge. 
Brief description of rationale behind each tool/language/framework of choice.
Brief description of key challenges. It’s okay to say you are not sure what you did was the best way to solve it. Be sure to justify your decision.
Note any caveats and potential failure scenarios.
Note what you would do differently in a production environment.
 
### Part A, GitLab and Webhooks
Create and deploy k8s manifests that contain the following applications using publicly available images from Docker Hub:
Rocket Chat
GitLab
*** Do not use a manifest package framework (e.g. Helm) for this step ***

*** Note: I have decided to not use the manifests for gitlab. The only working gitlab manifest I could find was from 4 years ago, version 8, where the current is 14. There is a good chance much of it doesn't work or is insecure. I will be making a lot of modifications to the helm charts, however. ***
* Write a REST API application that, when sent a GitLab webhook, sends the git commit SHA to a Rocket Chat user as a direct message (language of your choosing). Push this to a project on your GitLab instance created above.
* Write a Dockerfile and k8s manifest(s) for that application and configure GitLab CI such that it builds and pushes the image to the bundled GitLab container registry, then deploys to your k8s cluster.
* Configure GitLab CI to send a webhook to your deployed application, triggered by commits to the application’s GitLab project.
 
### Part B, Monitoring and Custom Metrics
* Deploy the Prometheus and Grafana to your k8s cluster using the kube-prometheus-stack Helm chart, saving settings to a `values.yaml`.
* Add a Prometheus metrics endpoint to your REST API that exposes a count of received webhooks.
* Configure Prometheus to scrape that endpoint using ServiceMonitor, and create a provisioned Grafana dashboard for the metric.
* Create another custom metric for your REST API that exposes a count of in-flight requests.
* Deploy a custom metrics adaptor for Prometheus, saving your k8s manifest(s) and/or chart `values.yaml`.
* Add an HPA manifest to your REST API that’s sensitive to the custom metric from step 1.