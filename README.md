# FFC Docker dotnetcore

This repository contains .Net Core parent Docker image source code for the Future Farming and Countryside programme.

Two parent images are created from this repository:

- `ffc-dotnetcore-runtime`
- `ffc-dotnetcore-sdk`

It is recommended that services use [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build) to produce production and development images, each extending the appropriate parent, from a single Dockerfile.

An [example](./example) is provided to show how parent images can be extended in a Dockerfile for a service. This should be a good starting point for building .Net Core services conforming to FFC standards.

## License

THIS INFORMATION IS LICENSED UNDER THE CONDITIONS OF THE OPEN GOVERNMENT LICENCE found at:

<http://www.nationalarchives.gov.uk/doc/open-government-licence/version/3>

The following attribution statement MUST be cited in your products and applications when using this information.

> Contains public sector information licensed under the Open Government license v3

### About the license

The Open Government Licence (OGL) was developed by the Controller of Her Majesty's Stationery Office (HMSO) to enable information providers in the public sector to license the use and re-use of their information under a common open licence.

It is designed to encourage use and re-use of information freely and flexibly, with only a few conditions.
