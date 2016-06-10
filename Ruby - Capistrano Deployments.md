## Ruby - Capistrano Deployments

By Jack Szwergold, October 15, 2015

### Deploy flow.

	deploy:starting    - start a deployment, make sure everything is ready
	deploy:started     - started hook (for custom tasks)
	deploy:updating    - update server(s) with a new release
	deploy:updated     - updated hook
	deploy:publishing  - publish the new release
	deploy:published   - published hook
	deploy:finishing   - finish the deployment, clean up everything
	deploy:finished    - finished hook

### Rollback flow.

	deploy:starting
	deploy:started
	deploy:reverting           - revert server(s) to previous release
	deploy:reverted            - reverted hook
	deploy:publishing
	deploy:published
	deploy:finishing_rollback  - finish the rollback, clean up everything
	deploy:finished

***

*Ruby - Capistrano Deployments (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*