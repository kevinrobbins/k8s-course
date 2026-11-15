# Kubernetes course project

## Image CI

[`.github/workflows/images.yml`](.github/workflows/images.yml) runs on pull
requests, pushes to `main`, and manual dispatches. It runs the client tests,
then builds all four production images and checks their Nginx configuration or
Node.js syntax. The API and worker do not currently define unit test suites;
their syntax checks do not test database connections or application behavior.

Successful image jobs on `main` publish the exact checked images to Docker Hub:

| Build context | Docker Hub repository |
| --- | --- |
| `client` | `krobbins99/k8s-course-client` |
| `server` | `krobbins99/k8s-course-api` |
| `worker` | `krobbins99/k8s-course-worker` |
| `nginx` | `krobbins99/k8s-course-nginx` |

Each image receives `latest` and `sha-<full Git commit SHA>` tags. Pull requests
and manual runs on other branches only build and check images; they do not log in
or publish. Images publish independently after their own checks pass; publication
across the four repositories is not atomic.

Before publishing, add these repository secrets under **Settings > Secrets and
variables > Actions**:

- `DOCKERHUB_USERNAME`: a Docker Hub account with write access to the repositories
  above.
- `DOCKERHUB_TOKEN`: a Docker Hub access token with write permission.

The image names match `docker-compose.yml`. If moving to another Docker Hub
namespace, update the `IMAGE` job environment variable in the workflow and the
image references in both Compose files.

The workflow uses Docker's [test-before-push pattern](https://docs.docker.com/build/ci/github-actions/test-before-push/)
and Buildx caching through GitHub Actions.
