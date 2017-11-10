# Continuous Delivery with Docker + LiFX
This repository contains all resources for a presentation at the HTL Leonding's infoday 2017.

- `compose` contains the docker-compose based CD stack
- `helloworld` contains a simple example project to put into the CD chain to showcase the LiFX extreme feedback / the automatic deployment


1. Make sure you have `docker` and `docker-compose` installed.
2. Deploy the continuous delivery chain based in `compose`.
3. Use the helloworld project to test the chain
   - Create a new repository in gogs
   - Set the upstream
   - Push the example project to gogs
4. Watch the chain:
   1. gogs call jenkins
   2. jenkins push to the registry
   3. jenkins notify the buildbulb
   4. the registry call the redeployer
   5. the redeployer calling docker-compose pull / up in the `compose/product` folder
