# FFC Docker dotnetcore

This repository contains .Net Core parent Docker image source code for the Future Farming and Countryside programme.

Four parent images are created from this repository:

- `ffc-dotnetcore-runtime`
- `ffc-dotnetcore-sdk`
- `ffc-dotnetcore-runtime-development`
- `ffc-dotnetcore-sdk-development`

It is recommended to use [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build) to produce production and development images, each extending the appropriate parent, from a single Dockerfile.

[Example](./examples) Dockerfiles are provided to show how parent images can be extended for different types of services. These should be a good starting point for building .Net Core services conforming to FFC standards.

## Example files

[`Dockerfile.web`](./examples/Dockerfile.web) - This is an example web project, that requires a build step to create some static files that are used by the web front end.

[`Dockerfile.service`](./examples/Dockerfile.service) - This is an example project that doesn't expose any external ports (a message based service). There is also no build step in this dockerfile.

## License

THIS INFORMATION IS LICENSED UNDER THE CONDITIONS OF THE OPEN GOVERNMENT LICENCE found at:

<http://www.nationalarchives.gov.uk/doc/open-government-licence/version/3>

The following attribution statement MUST be cited in your products and applications when using this information.

> Contains public sector information licensed under the Open Government license v3

### About the license

The Open Government Licence (OGL) was developed by the Controller of Her Majesty's Stationery Office (HMSO) to enable information providers in the public sector to license the use and re-use of their information under a common open licence.

It is designed to encourage use and re-use of information freely and flexibly, with only a few conditions.
