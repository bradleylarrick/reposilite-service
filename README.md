<!--
  Copyright (c) 2024 Bradley Larrick. All rights reserved.

  Licensed under the Apache License v2.0
  http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
 -->

# Reposilite as a service on FreeBSD

This is a service script to run Reposilite on FreeBSD. It has  been
validated with Reposilite 3.5.10+ on FreeBSD 14.0-RELEASE-p6.

- [Installing Reposilite](#installing-reposilite)
- [Version Notes](#version-notes)
- [License](#license)

## Installing Reposilite

1. Start by creating a pseudo user for running the Reposilite service. Since
the user is created only to run the service, you can set the shell to nologin and
skip the password:

```
$ adduser
Username: repo
Full name: Reposilite
Uid (Leave empty for default):
Login group [repo]:
Login group is repo. Invite repo into other groups? []:
Login class [default]:
Shell (sh csh tcsh bash rbash git-shell nologin) [sh]: nologin
Home directory [/home/repo]:
Home directory permissions (Leave empty for default):
Use password-based authentication? [yes]: no
Lock out the account after creation? [no]:
Username   : repo
Password   : <disabled>
Full Name  : Reposilite
Uid        : 1002
Class      :
Groups     : repo
Home       : /home/repo
Home Mode  :
Shell      : /usr/sbin/nologin
Locked     : no
OK? (yes/no) [yes]: yes
adduser: INFO: Successfully added (repo) to the user database.
Add another user? (yes/no) [no]: no
Goodbye!
$
```
2. Create the directories required for the service and set the owner to the ID created
above:
```
$ mkdir /usr/local/reposilite
$ chown repo:repo /usr/local/reposilite
$ chmod 750 /usr/local/reposilite
$ mkdir /var/db/reposilite
$ chown repo:repo /var/db/reposilite
$ chmod 750 /var/db/reposilite
$ mkdir /var/log/reposilite
$ chown repo:repo /var/log/reposilite
$ chmod 750 /var/log/reposilite
$
```
This first directory is for setting up the application, the second is for the
application database and the third is for logging.

3. Download the latest version of the Reposilite jar from https://github.com/dzikoysk/reposilite/releases
into the application directory created above (`/usr/local/reposilite`). Set the
owner and permissions of the jar file:
```
$ curl https://maven.reposilite.com/releases/com/reposilite/reposilite/3.5.11/reposilite-3.5.11-all.jar > /usr/local/reposilite/reposilite-3.5.11.jar
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 71.9M  100 71.9M    0     0  4019k      0  0:00:18  0:00:18 --:--:-- 4258k
$ chown repo:repo /usr/local/reposilite/reposilite-3.5.11.jar
$ chmod 640 /usr/local/reposilite/reposilite-3.5.11.jar
$
```
4. Create a symbolic link for the jar file (this makes upgrading easier):
```
$ ln -s /usr/local/reposilite/reposilite-3.5.11.jar /usr/local/reposilite/reposilite.jar
$
```

5. Copy a configuration file into the application directory (`/usr/local/reposilite`).
A sample script is provided with this project. Update the script as appropriate (usually
just changing the service port value).
<br>
<br>
6. Copy the `reposilite` script into the `/usr/local/etc/rc.d` directory and add the following
to `/etc/rc.conf`:

```
...
reposilite_enable="YES"
# Parameters
# reposilite_user
# reposilite_jar=
# reposilite_config=
# reposilite_wd=
# reposilite_log=
...
```
The default values for the parameters (which match the values used in these instructions) are:
```
reposilite_user="repo"
reposilite_jar="/usr/local/reposilite/reposilite.jar"
reposilite_config="/usr/local/reposilite/configuration.cdn"
reposilite_wd="/var/db/reposilite"
reposilite_log="/var/log/reposilite"
```
The Reposilite service can now be started and stopped using the `service` command:
```
# To start the service:
$ service reposilite start
$
# To stop the service:
$ service reposilite stop
Stopping reposilite.
Waiting for PIDS: xxxx.
$
# To re-start the service:
$ service reposilite restart
Stopping reposilite.
Waiting for PIDS: xxxx.
$
```
## Version Notes

### Version 1.0 &mdash; 2024-05-01

- Initial release.

## License

Licensed with the [Apache 2 License](/LICENSE).
