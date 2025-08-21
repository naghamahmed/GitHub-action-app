# GitHub Actions CI/CD Lab (Flask → Docker Hub → optional EKS)

## 0) Prereqs
- GitHub account + new repo (e.g., `flask-gha-lab`)
- Docker Hub account + access token (Settings → Security → New Access Token)
- (Optional) AWS EKS cluster and an IAM Role for GitHub OIDC

## 1) Get the starter
Upload this folder to your new GitHub repo **or** clone your repo then copy files.

```
app/
tests/
k8s/
.github/workflows/
Dockerfile
requirements.txt
```

## 2) Run CI
Commit & push to `main` (or open a PR).  
Watch **Actions → CI** run matrix tests on Python 3.10 & 3.12.  
Artifacts include `coverage-<pyver>`.

## 3) Build & Push Docker
Add repository **Secrets** (Settings → Secrets and variables → Actions):
- `DOCKERHUB_USERNAME` = your Docker Hub username
- `DOCKERHUB_TOKEN` = your Docker Hub access token

Create a tag and push:
```
git tag v0.1.0
git push origin v0.1.0
```
Workflow **Build & Push Docker** will push:
`DOCKERHUB_USERNAME/flask-demo:v0.1.0`

## 4) (Optional) Deploy to EKS with OIDC
- Create an IAM role trusted by GitHub OIDC and allow EKS access.
- Put the role ARN into `deploy-eks.yml` under `role-to-assume`.
- Ensure your cluster name/region match `update-kubeconfig`.

## 5) Verify
Local Docker run (optional):
```
docker build -t test-flask .
docker run -p 5000:5000 test-flask
# open http://localhost:5000
```

EKS:
```
kubectl get svc
```

## 6) Troubleshooting
- Secrets unavailable to PRs from forks → use trusted branches or environments.
- Image not found → check Docker Hub repo name/tag and login step.
- RBAC/cluster errors → verify IAM role permissions and cluster settings.
- YAML errors → check indentation; inspect Actions logs.
