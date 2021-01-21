# Context Config Tool (ContextCF)

This application is intended to provide a contextual configuration in a stateful application.

According to the current `$HOSTNAME` (which is provided by the stateful-app grade Kubernetes CRD _StatefulSet_), `getcontext.sh` will choose a configuration file and source it. Using such mechanism, it will determine the set of environment variables to be published in the environment.
