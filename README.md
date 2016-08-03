## PHP7 and Gearman on Ubuntu 16.04

Just proving that this works. The important bits are in the shell provisioning in the Vagrantfile.

After you run `vagrant up`:

* `vagrant ssh`
* `php pecl-gearman-master/examples/reverse_worker.php`

Then, in a separate terminal:

* `vagrant ssh`
* `php pecl-gearman-master/examples/reverse_client.php`
