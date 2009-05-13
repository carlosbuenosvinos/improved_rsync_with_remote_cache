# Capistrano rsync_with_remote_cache

This gem provides a deployment strategy for Capistrano which combines rsync with a remote cache, allowing fast deployments from Subversion repositories behind firewalls.

## Requirements

This gem requires Capistrano 2.0.0 and higher. Git support requires Capistrano 2.1.0 or higher.

This gem has only been tested with Subversion and Git. It is unlikely to be supported for other SCM systems unless we adopt them at Viget Labs, or get outside help.

This gem requires and `rsync` command line utilities on the local and remote hosts. It also requires either `svn` or `git` on the local host, but not the remote host.

This gem is tested on Mac OS X and Linux. Windows is not tested or supported.

## Using the strategy

To use this deployment strategy, add this line to your `deploy.rb` file:

    set :deploy_via, :rsync_with_remote_cache

## How it works

This strategy maintains two cache directories:

* The local cache directory is a checkout from the SCM repository. The local cache directory is specified with the `local_cache` variable in the configuration. If not specified, it will default to `.rsync_cache` in the same directory as the Capfile.
* The remote cache directory is an rsync copy of the local cache directory. The remote cache directory is specified with the `repository_cache` variable in the configuration (this name comes from the `remote_cache` strategy that ships with Capistrano, and has been maintained for compatibility.) If not specified, it will default to `shared/cached-copy` (again, for compatibility with remote_cache.)

Deployment happens in three major steps. First, the local cache directory is processed. If it does not exist, it is created with a checkout of the revision to be deployed. If it exists, it is updated to the revision to be deployed.

Second, rsync runs on the local side to sync the remote cache to the local cache. When the rsync is complete, the remote cache should be an exact replica of the local cache.

Finally, a copy of the remote cache is made in the appropriate release directory. The end result is the same as if the code had been checked out directly on the remote server, as in the default strategy.
