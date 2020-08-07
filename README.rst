thoth-graph-refresh-job
-----------------------

A job for scheduling solver to resolve dependency graphs and package-analyzer to gather digests and ABI of new packages or packages not analyzed yet.

Running the job locally
=======================

You can run this job locally. The job will talk to OpenShift API to schedule
jobs in Thoth's middletier namespace and will check for packages which were not
analyzed yet by talking to a cluster Dgraph's instance:

.. code-block:: console

  $ pipenv install  # Install all the requirements
  $ oc login <cluster-url>  # Make sure you are logged in
  $ KUBERNETES_VERIFY_TLS=0 THOTH_INFRA_NAMESPACE=thoth-test-core GRAPH_SERVICE_HOST=graph.test.thoth-station.ninja GRAPH_TLS_PATH=./tls-test pipenv run python3 ./app.py

You need to obtain TLS certificates in order to talk to a cluster graph
database and place them into `./tls-test` directory as shown above.

Insights to graph-refresh job
=============================

The job is run periodically as OpenShift's CronJob. It can be also triggered
automatically by Thoth's monitoring system when there is no workload happening
in Thoth's middletier namespace.

.. note::

  * graph-refresh job is run in Thoth's frontend namespace
  * templates for solvers are picked from Thoth's infra namespace - see installed solvers endpoint on Management API for available solvers in the deployment
  * solvers are scheduled in Thoth's middletier namespace
  * kafka producer ensures that the messages to schedule solvers and reverse solvers(revsolvers) are sent to the kafka broker

Packages which are not resolved yet might be coming from:

* user requests on advises for software stacks
* user requests for provenance checks
* container image scans
* explicitly registering Python packages to Thoth on Management API endpoint
