# Ansible Complete Notes — Beginner to Advanced

> Converted from the uploaded `Ansible.pdf` for GitHub documentation.
>
> **Note:** The source PDF is handwritten/illustrated and contains diagrams and code examples. This Markdown version uses OCR, so verify YAML indentation, commands, and code snippets against the original PDF before running them.

## Contents

1. Introduction to Ansible
2. Ansible Architecture & Installation
3. Inventory & Ad-Hoc Commands
4. Playbooks & YAML Basics
5. Ansible Modules
6. Variables & Facts
7. Conditionals & Loops
8. Handlers & Tags
9. Ansible Roles
10. Templates & Jinja2
11. Ansible Vault
12. Error Handling & Blocks
13. Ansible Galaxy & Collections
14. Real-World DevOps Workflow
15. Ansible Best Practices
16. Common Interview Questions
17. Common Mistakes & Troubleshooting
18. Quick Revision Cheat Sheet

---

<!-- Source PDF page 1 -->

-ANSIBLE:
COMPLETE NOTES
> (Ansible) <= —

@
ANSIBLE
_ = ea —F

---

<!-- Source PDF page 2 -->

Introduction to Ansibl
oe —eee—eeeeeeeeeeooOEoaoEOESoolSSsaeees
nua}
e ° : in | e
@ What is Ansible? Y: | @) Why use Ansible?
Ansible is an open-source IT automation VY Simple & easy YAML syntax
tool that helps you configure, deploy Vv Agentless - no software to install
and manage systems easily. Y Consistent & repeatable automation
It uses SSH to communicate and VW Idempotent - safe to run multiple times
does not require any agent on V Fast and lightweight
managed nodes. VY Large community & enterprise ready
® Key Features oR, | @ Agentless Architecture
* Agentless architecture (uses SSH) Ansible connects to remote nodes using
© Push-based - no agents on nodes | SSH. Ne agents or ——
¢ YAML based Playbooks ua a
e gee & poe | ca SSH en Managed Node 4
+ Strong module sp a |e
© Powerful automation with minimal setup ! (Ansible) © _ > Managed Node 2
e Extensible with Roles, Collections & Plugins ‘y Node 3
©) Real-world Use Cases
ooo q >
2 GD ee
Configuration Application Infrastructure Continuous Monitoring &
Management ; Deployment Provisioning ' Integration/Delivery | Maintenance
Hee e e °
; , Simple Architecture Diagram
WY Interview Tip | Web Server
Remember: Ansible is agentless, | [| a
| uses SSH, and works on a | ee es —> EQ) pp Swe
| push-based model using modules | Apsible Control Node Encrypted
and playbooks. (Your Machine) Connection Ny DB Server
Nee ees ee ee er eee

---

<!-- Source PDF page 3 -->

° ° °
Ansible Architecture & Installation
le

“> ‘Andlle filles NOENTLESS ‘architecture, “27 Node 7 Web Serve
7
> A single Control Node manages A] <__ Managed Node 2
one or more Managed Nodes Py a fea} App Server
over SSH. _ =
No agents or additional software ~S Managed Neda 3
. | (fe) DB Server
required on managed nodes. pea
| an es a a Te
| [R) Lrventory | | oe Uses SSH (port 22) |
(Defines Hosts | | to execute modules
(___ 0m Groupe) ] and return results, |
(2) Key Components (3) Installation (Linux)
VY Control Node : Machine where Ansible is installed. # Update system
V Managed Nodes : Target systems managed by Ansible. sudo apt update -y
V Inventory : List of hosts and groups. # Install Ansible
SF SSH + Sscure sie channal’ sudo apt install ansible -y
rT pee # Verify Installation
’ ansible --version
@) Verify Installation 6) First Command _
ansible --version # Ping all hosts from inventory
ns file = a eiantlin ansible all -m ping
config file = /etc/ansible/ansible.cfg ‘
configured module search path = “> Expected Outpt:
/usr/share/ansible/plugins/modules server1 | SUCCESS => {“ping": “pong"}
python version = 3.x.x wy, server2 | SUCCESS => {“ping”: “pong"}
GO gg oe ee a
| VW Real-world Use Case | -) 4~ Interview Tip
| * Update packages, deploy applications, | : Ansible is agentless, uses SSH, YAML |
| configure servers, manage users, | Playbooks, and is idempotent. |
{ monitor services - all from one place! } 1 These are key points in interviews! }

---

<!-- Source PDF page 4 -->

Inventory & Ad-Hoc Commands
i i i i il Ctl ie aii iii ati tii ti ti ati ti i i ti i i el
@) Inventory File @) Static vs Dynamic Inventory
— |
Inventory is a file that lists the {— | W Static Inventory: Manually created
hosts or nodes on which Ansible | file (INI or YAML). TL
wil run. TE can be static (INI/YAML) | Dynamic Lwentory: Generated 0}
or dynamic (cloud plugins, APIs). | on yen or plugins
’ zure, » °
(3) Host Groups | @) INI vs YAML Inventory
Orgorias Ios taka geigeforeuny | INI Format: (Tniitonal) | __ WAML Ferma (Madera)
| webOt ansible host 10.0.0.1 | | mre |
| Cot) | | seta ansible_host=10.0.0.2 | | haste: i
| 1 | web01: { ansible_host: 10.0.0.1 }
Child Groups | | [ab] 1; | web02: { ansiblehost: 10.0.0.2 }
under | | db01 ansible host = 10.0.1.4 | | db:
abot | 02 | ab01 a102 | Parent Group | | db02 ansible_host= 10.0.1.2 | | ge { ansible_host: 10.0.1.1 }
| | [ db02: { ansiblehost: 10.0.1.2 }
(5) Common Ad-Hoc Commands
ping ! command ! shell copy service
| — | —— | 1
()) re | 1 (S|
7 | Op ] ee
(anatile all -m ping} || ansile wah -m command! { ansible web -m shell! || ansible wah -m copy || ( cnsible web -m anna |
er I -a “uptime” Ht -a “df -h" if ~a “sre= /tmp/a.txt ir | a “name=nginx i
TTT _ Mest dtnpactet” J |__statenatarted”
C...._ > —_—_—__
am. % Real-world Use Case ! @ Interview Tip |
a" Use ‘oad to de per | |
qi: - iia A a haa yo on | Ad-hoc commands are great for quick |
“ con db servers with a single playbook or tasks, but Plasghooks os preferred for |
= ad-hoc command, /_ ee

---

<!-- Source PDF page 5 -->

Playbooks & YAML Basi
J MAgROOKRe & Fi. Dasit
@ What is a Playbook? | (2) YAML Syntax Basics
I
A Playbook is an Ansible automation |W Indentation matters (use 2 spaces)
file written in YAML. It defines | W Use "-" for list items YAML is
what to do, on which hosts, and ‘Dalles, teas ra ae
in what order (tasks). | V Case sensitive maintain.
Y Comments start with #
! |
® hosts ! @ tasks © become
O Specifies the target | A list of steps Used to perform
aan hosts or host groups — (tasks) executed | tasks with
from the Inventory | y—]| sequentially on | elevated privileges
where the playbook | the target hosts | (like sudo/roct).
will run. H using Modules. |
! |
© Si Playbook Ex How a Playbook Works
__- === Play name > Ey test 1
= name: Intell end start Nooo bare E-+s>-< —
hosts: webservers  <-—-—————-|-~ Target hosts /groups, | === = Host 2
basinn from Inventory Control Node “Say
: yes *-- ; : Se com
tasks: ~—-+— Run tasks with (Ansible) FE] Host 3
- name: Install Nginx package sudo privileges (pF
yum: __ List of tasks | w Real-world Use Case |
name: nginx <¢-------" to execute | Deploy Nginx on multiple web servers,
state: present configure it, and ensure the service H
~ name: Start Nginx service _ Install Nginx | is always running using a Playbook. J
service: AAA A AMT if not present Soro
comes gis @ Interview Tp )
state: fiarted ____|_- Start service Playbook bring repeatability, idempotency
— va ate & and consistency to infrastructure
| automation. LC
|i ean aia aan Riera. Nak)
~) Key Takeaway Playbooks + YAML are the heart of Ansible Automation.
H Define once, run anywhere! wa }

---

<!-- Source PDF page 6 -->

Ansible Modul
—_—_———eeeweeees Sc 0 oe ow...
(4) What are Modules? | () Why use Modules?
Modules are reusable units of VY Built-in and easy to use (GALA
code in Ansible that perform | W Idempotent by default
They make Playbooks simple, | 7 Consishont ond reliable cutomation fara}
powerful and idempotent. | W Large collection in Ansible Galaxy
©) Commonly Used Ansible Modules
___Medule ____i____ Purpose t_ Example Usage i Notes
¥ ! Runs commands | ansible web -m command | W Use for simple commands.
command without shell features. -a “uptime” More secure.
-------------4--- ~~ -- +--+ 4 - = + - - - - - +--+ -
hell | Runs commands | ansible web -m shell | W Use when you need shell
=» copy | nie ager | =a sre=/tmp/app.conf ! For big files use
ae RO OP. I OE
| M files, directories, | ansible web -m file | W Create/delete files or dirs
j Senge | | ,
. ee | =a “path=/opt/ ‘ssi
Be a ee |
1M | ansible web -m service | ¥ Works on most Linux
10} Service | (start/stop/restart). i a eyes" | distributions.
gam a | Ym Su a i
' Installs, removes or | ansible web -m package , | % OS independent. Uses
Bo package manages packages. -a “name=nginx state=present underlying package mgr.
ee ee ee Se eee
! | ible web -m user !
| Manages user accounts | Snst | W Create, update or remove
user | F | -a ‘name= state=, 1
a t and attributes. | ” ae eta | users and groups.
aaa ae fe |... SEE EE
| Package management | ansible web -m apt » |v Use Debian/Ubuntu,
apt /yum | for Debian/RHEL based | ~* “namengit staterpresent” — | an RHEL/CentOS.
I | ansible web -m ' “mn
! egrome, Ine “name~ git | '
gre &) Real-world Use Case ( “sy “Interview To 2 tti<~ Ci CN
cae Using modules, you can deploy an H re |
aa] install packages, copy configs, start | Know the purpose of each module |
in the desired state (Idempotent), Ke wheal 05) <5

---

<!-- Source PDF page 7 -->

Variables & Fact
I
(@) Variables in Ansible | (2) Types of Variables iy inventory/
|
Variables store data that can be | Y vars: Defined in Playbook BB group_vars/
reused in Playbooks. They make | © host_vars: Variables for specific Host L © all.ymi
Playbooks flexible and dynamic. W group_vars: Variables for Host Group Bp host_vars/
|
x = 10 WV extra_vars: Passed from CLI Ly webOt.ymi
name = web Variables | : : © a
pea re | W~ facts: Automatically discovered data
ae
® Facts in Ansible | Common ansible_facts
——— | “WF ansible_hestname |
Facts are system information collected | ¥ ansible_os_family |
in ansible_facts variable. | ¥ cnsible_mamory_mb
ea) | ¥ ansible_processor_vepus |
Retrieves system information | and many more... |
from hosts ei i
@ Example Usage © erie Prcdence igh Low
Pee Saag ae ek en es Higher
[- ae oe dQ) extra_vars (CLT) Priaity
gather_facts: yes F S11 @) task vars
vars: | Using Variable ||
| ports 80 a 1! (A) role vars (and defaults)
| crane: Show OS Fonds!” | (hetar af || Sarees
| * Sie. ey iets | (6) host_vars (host level)
| msg: “OS Family is {{ ansible_facts.os_family }}" | | (7) group_vars (group level)
{ alah: pane | | © = dofeutts aint
i aA =o, . «4 gi> Real-world Use Case
| | ‘4h e
| w Tkerview Tip vy | HH Use variables for configuration values (ports,
| Facts are dynamic and collected from ‘ ss | 17, Las paths, package names) and facts for making
| hosts at runtime, while Variables are | fee} decisions based on server information
| defined by us | (OS, memory, interfaces, etc.).

---

<!-- Source PDF page 8 -->

Conditionals & L
(2) Conditionals (when) | is defined / is not defined
Use when to run tasks only
when a. condition is true. | © is defined —» Check if variable is available
ee en ee ere | © is not defined =» Check if variable is micsi
(Common Tests ow N | * ‘ —
| ¥ is defined > vriale iste | ——$——
[A led > ele dan vl [ He Sain T |
v os, fn “> equality check | . |
i | { commen, wales allie etirviene. |
@® Loops ® Register Output | ©) Accessing Registered Data
a | of teak and uss i in next tasks, | ° "esult.ctdout ape
| a a . . .
Loop Examples | i Registered Data | | © revl.sdeck lines > List of ie
| loop, withitems, loop_control | i St aia Mantabla and ew ke | @ result. rc — Return code
(loop is the modern way) | it accessed using .stdout, rc, © result. results — For loop items
©) Practical Examples
AANA Aa MAR | LEE || aaa acca;
{ 1. whan + is defined iif 2. Loop Example Vif 3. Register Example
~ anes Tua Mine plume i dfoad) || | Sawa: Cradke malliale sours 1 | = name: Check disk asoge |
ia | | user: . a shell: df h
| namei “{{ phg-none })” H i name: "{{ item }} | | register: diskresult |
| shake: ‘pease LTT gant Prevent | 11 | mame: Show dish output |
| an - 40 1 | ars Gihizreniih teh lines
| se Case } jer 11) ue cum @) |
| ; ’ j tl Itt ° |
| Install « package only when the variable 1 | Use Cases | | | uasemaapaea am @
[er @) aa... A |= S
Shy Real-world Use Case QD) Best Prachice
. Aleta lente I a Ties dig cca |
fw] —> {o> —> FE oe | «AF Une vagicer te eveld, re-ruming tacks |
Autile Hanged Hs J Tanprlak  ral |W Combine when with facts/variables
ashe Aihimadten C)) | ¥ Keep conditions simple & readable |

---

<!-- Source PDF page 9 -->

LIARGtLers O& 1Aags
(@) Handlers (What & Why?) | @Q) Handlers Example
only when notified by other tasks. | template:
Thay ere cmmouted of the and. of | sret_nginx.confj2 wT. n.
a Play. H | dest: /ete/nginx/ nginx. conf 4 This handler
aM le | notify: Restart Ngine rans only if
| ¥ Run once, even if notified multiple times we nam Mold. Ml task makes
tap ntey en i | proerien a mage
——— | dishes vaste
\ .
ORC | @ skip-tags
Tags help you run or skip specific | Use --skip-tags to exclude tasks
parts of a Playbook. with specific tags.
fel, al Run with tags titi«<SY | Example ‘
- name: Install Naginx ensiblss sile.ynd =-tags “install” a site.yml --skip-tags “debug, test”
apt: # Run only tasks with lag ‘instal’ | | # All tasks will run — those
age peel | ansible-playbook site.yml --skip-tags “web” | ire: Bee
tags: # Skip tasks with tag ‘web’ 8 7 ve-neeeee —~-—-- ~,
= web cnale-pinghenk tinge —tegs “ite | | is, bag long! take (ills, Dealing, dhniipale) |
| # Run multiple tags | j ce , H
~—-————— aaa - — : y | Set you can run or uae easily. ; ; }
6) Execution Flow (Play + Handlers)
Start Play Run Tasks Notify Finish all Run Play
Be -vhlerenll ( (ep) Radavel Ue Coe
+ Tks ran in dr | age wl wa fis
| © Tf « task notifies a handler, i is marked: | | bh me ees cng Co: oe males © Soe |
1) Mout. it the end if noti
Tags help in selective execution, making Playbooks flexible and powerful

---

<!-- Source PDF page 10 -->

Ansible Rol
tt et et et tt i et

(@) Why Roles? | @ Directory Structure

Roles help you organize and reuse | roles/

Ansibta cobesk in & sendard | syed

structure. They make Playbooks tasks/ # Main list of tasks

clean, modular and easy to maintain. | handlers/ # Handlers (notify)

CoC. ~~ oo | vars/ # Role variables

| Benefits dofeulic/’ # Difealt: varlibles

ly Reusability templates/  # Jinja2 templates

| V Standardized structure “gi files/ # Static files

| ¥ Easier collaboration i | meta/ # Role metadata

¥ Separation of concerns H — | baits # Role testing

| v Publish & share via Galaxy | | README..md # Role documentation

) Role Directories Explained

[Directory | Parpmee Emample

. # roles/myrole/tasks/main.yml
| if | ee oe mes he |
| exec by rere. apt: name=nginx state=present
q - e # roles/myrole/handlers/main.yml
fia llers/ Define handlers that run a ue
when notified. service: name=nginx state=restarted
{x} J Variables with higher # roles/myrole/vars/main.yml
wis precedence (role level). nginx_port: 80
| Default variables with low # roles/myrole/defaults /main.yml
| defaults / precedence (overridable). nginx_port: 80
B Jinja2 templates for # roles/myrole/templates/nginx.conf. j2
nad s/ dynamic configuration. server_name {{ server_name NE
4 ‘les / Static files copied | # roles/myrole/files/motd.txt
f as-is to managed hosts. (Hello from Ansible!)

i : ta. \
YY Interview Tip | PB — Real-world Use Case |
Roles enforce best practices and make Automation | bra Use roles to automate common components like |
scalable. Know the directory structure and variable — | [vs] Nginx, MySQL, Docker, and Users across |

---

<!-- Source PDF page 11 -->

=
3 =
lemplates & Jinja2
= '
(1) What are Templates? | @ How it works?
= see to be generated using Jinja2 J | <>| — —_
syntax and variables. |
, Template Jinja2 Rendered
meen final file on the | C2 fila) Engine File
MB managed Host. |  Ansible reads the template, replaces variables
r' | on the target Host.
Fam) Variables in Templates | CG) Filters in Tinja? |) Lowpe in Templates
prugy Use {{ variable_name } to insert | Filters help to transform or format data. | Use for loop to iterate over lists.
: OT | peer mn nny | pon 5,
pag || eft) Daft value 1 | ( Enample: usors.tut.j2 |
Py |! Py fg ele Ramee mee 1 EC asrsname Hel aaa uid 3 |
} = ° | Example: w Interview Tip cabs
= © filters and loops well!
PME Use if, elif, ase for conditional logic. | © Froctical Example (Rayhook)
rns | To ant ateleign wnligndinten hells tapller haat pS
pe foo |e a
pe | CS || pL...
ver, . com; 1 | rap 80 > | server_nama {{ Ue
OT rn 8 eg ee Z| oat det Dh
{% endif %} —_ H | tasks: / \ Jaa |
ee en Rl eel H v
(RR Realzworld Use Case | | a | —_ Ganarated File on Host _ |
bee: | CEE) Gaasica cogsrn fun fei | | dest: /eke/aginx/nginx.conf we
==] Kubernetes, Application configs, | | group: root ent
= and environnent-specific settings | | mode: '0644' root /var/unna/himl
across Dev, QA, Prod. | a |
=
. Best Practices
7 o Keep template files in templates/ directory. ple-eormie ec et Ades W Use filters to keep templates clean.
. Y Use meaningful variable names. Y Avoid complex logic in templates. Y Test with -~check before running.

---

<!-- Source PDF page 12 -->

ible Vault
; Ansible Vau
ee eeealEESUSESo™Wwe

=

(2) Why Vault? | How Vault Works?
= Ansible Vault helps you encrypt \
fees sensitive data like passwords, Encrypt .
ME APT keys, tokens, certificates, etc. </> at — | </>
Me variable files SAH Plain Text Enerypted File
. ee Sensitive Data, e to
. | Benefits ) Vault | : ) (Sofa. to stere)

o Protects sensitive information I :
- [at Spcieteceie apecion YY Interview Tip
= ly Easy to mae Regio Always encrypt sensitive data before committing to

[a | Git, Never store plain text secrets in Playbooks.
=

@ Vout Commande
PMD (Encrypt «Fle \[ Decrypt Fle Edit a File | View a File \{ Rekey a File

a

~ | @ 0 ® a

| ansible-vault encrypt file.ynl |} ansible-vault decrypt file.ymt | | onsible-vault edit file ynl | ansible-vault view file.yml | | ansible-vault rekey file. yml
=
a | > Pompe for Vt pment |} in pain tn meee pes | stent ang | | fr oe ening fe
PAG) Password Fle (Best Practice | © Best Practices
= Store your Vault password in a file and [-) all Minn did
ha | Use Password Fla | |v Use passord file instead of [2
= | — ~-vault-password-file vault_pass.txt <command > typing password repeatedly.

aensible-veult --vault-password-file vault_pass.txt encrypt file. | v Limit access to password file

jt eset tS metireestt ot Rat SEP) | oF ite Waal Te fir mullite aE
ae Pu Sr | Wo Always test Playbooks after <A,
= our posniend fle ah oak (pormiaclnes (gn ote eae bud | . . F v—

| © Never commit the password file to Git. er is | decrypting to avoid failures. =]
begey * se ignore to exclude it mc)
: B® i eee Cai Example (Encrypt & we “nt

dh You can database passwords, cloud access . API tokens, Ener, in

| OD eae oe deed cd he ele D with Vault a) Paybeck Ss} |
= | =") across Dev, QA, and Pred environments. || vereyml vars.yml silage |

a 2, COs a os

---

<!-- Source PDF page 13 -->

ing & Block
Error Handling ocks
eee eee ee me ows"
i
PUD Syneres errors for @ task end | Define custom conditions that mark ©
ay ee cecaceess fet
- name: Run risky command | - name: Check disk space ]
i command: /bin/false | shell: df -h/ |
a ignore_errors: yes  # Continue register: disk i
; coven if this fails | | failed when: disk.stdout. find("100%') = -1 |
SS | Se Se SS ee ee
= ' —
~~ @) changed_when Ga | @ Block / Rescue / Always
ME Override the default changed status A Gronp tasks and Kandla errore grecefilly.
= with a custom condition. _« ud
= command: /bin/touch /tmp/test. txt command: /bin/false | —
register: result - name: Another task | tasks
4 changed_when: false # Always show command: echo “Success” |
no change _ rescue: . Runs only
rr 7 oe Hendin: fates if block fails
Retry a task until it succeeds. (#)) always:
Ly ( ~ ramet: Wait for service to staré a mais “This vans ae willinr shal |
> | shell: aystemetl is-active nginx a
register: sve
= | retries: 5 # Try 5 times
until: sve.rc == 0 # Success when rc == 0 } |
= SF }
© Execution Flow with Error Handling
= PY Interview Ty
Rescue Always ?
: # &} Playbook to try/catch/ finally.
: No (2) Use it to make Playbooks
Ls EE ee
Best Practices { YAY. Real-world Use Case |
= Do atnchinn iron W Use blocks to handle complex failures Y: en dalgg ah wee wes =
7 Use changed_when to heap idenpotency, VY Auays provide meaningful massages in recone, mag. ts te dt |
a oS Avoid ignoreerrors unless absolutely needed. o Use retry logic for unstable services/APLs. sllaieh widinalil digas
a Sy a ER ARSE TAP RIND 25S EC

---

<!-- Source PDF page 14 -->

= e e
. 1 tnsible Galaxy & Collections
=
: Galax | Collections
Ansible Galaxy is the official hub for | Collections are the Seer
"lke Roles and Collections. (a=) | format introduced in
“3 t—) | Ansible 2.10+.
OO ja ea oa
yg [What you get on Galery? | | {Why Collections?
| ¥ High quality Roles & Collections | |v Bundle of modules, plugins, roles, docs & tests |
SS | W Easy to integrate into Playbooks | |_¥ Standardized and future-ready content =
; | ‘
~~ Q) Install Roles from Galaxy | (4) Install Collections
a a ee a a Ae Sees Se
{ # Thstall role Od | { # Install a collection
‘- | ansible-galaxy install geerlingguy.nginx | S A" ansible-galaxy collection install community. general |
: # Install specific version | # Install multiple collections |
PM onsible-galary install. geerlingguy.nginx:2.2.0 | ansible-galaxy collection install \ j
MM Installed Location (default) ansible. posix } COLLECTION
bee (| ~/-ansible /roles/ |. SecBilled cacti (default)
, S—geotingey ng! H (" ~/.ansible /collactions /ansible_collections/ |
tasks/ | L— community / |
~ defaults / | earl? |
| meta/ ... } L— ansible/ |
~ SS ee | L_ posix/ |
= ‘6) Reusable Automation a
Using Galaxy content saves time and ensures best practices. | w Interview Tip
he) © Focus on automation, not reinventing (h the wheel! ee Galaxy helps in reusability,
~ Chang) Your “a i Infrastructure PS ge
———>) ——> P
~ (Roles & Collections) -_— future of — content
™ roles/ management. |
es Dev | QA | Prod
m @ L RS cccc le
= | Best Practices (FY\ Real-world Use Case
: oY Always use trusted publishers o Keep Galaxy content updated NL, Use community.general to manage
= Y Pin versions for stability a7 Doles Canes periges <0 emt geomet ee ati ccndistaille,

---

<!-- Source PDF page 15 -->

7
~~ ~— Real-World DevOps Workflow
: Git + Jenkins + Ansible : Deployment Pipeline & Configuration Management
~ Git (Source Control) | Jenkins (Automation Server) | Ansible (Automation Engine)
= | Wo Store code, Playbooks, | ao Automate build, test o Configuration Management
he, | ¥ Version control & | | Trigger Ansible Playbooks | W Idempotent deployments
> | collaboration | ~ Generate reports & | | oS Consistent across environments |
\ oY Branching & pull requests | | notifications | }
= Scene Se Ne aptamer NS pee erpeeteeerrieeentientenr tienen
CI/CD Workflow Diagram
: 2) @ ©) @ © ©
agg {Cede Comm Push to Repo Jenkins Trigger Build & Test Ansible Execution Deployment
aH & b @ a2 + © | fe
- &
© Developer writes code| | © Code pushed to © Webhook triggers © Build application © Jenkins triggers © Application deployed
= & updates Plagbeoks remote repository Jenkins job © Run tests / lint Ansible Playbook successfully to target
in Git repository. (GitHub / GitLab / automatically, * Validate Playbooks © Configure / Deploy servers / cloud /
: Bitbucket). (ansible-Lint) infrastructure ccndiainare:
i
e tH gays tid. ——<—_a
gy
Praggg v Ensure systems are in desired state a -~ -
ae np iis QOBes
= Wo Maintain consistency across Dev, QA, Prod I _ On-Prem Cloud VMs Containers Network
Wo Enforce security & compliance Run multiple times, | a Depl Validat Moni
= o Drift detection & remediation mel, | : => Cats = > i
s ee Agentless, SSH based, Simple & Powerful!

Example Pipeline (Jenkinsfile + Ansible) Se enany
be { pipeline { \ [ ~ beter weteorer | WY Use Roles & Collections for reusability |
sen { | anne: Task igi | 7 Use Jenkins Credentials securely

= stage(“Checkout") { steps ( git ‘rapo-urt” ) ) | | package | ¥ Validate Playbooks (ansible-lint)

. stage “Build & Test") ( steps { sh ‘mm clean test’ ) ) cake ted oS Use Inventory groups & variables

stage(‘Ansible Deploy!) ( | = :aasaar Cong App Pils | oS Implement notifications (Email/Slack)
- stops ( es | we ee ee 3
th ‘cnciblenplagbook <t imantory, deploy yal! dastt Ast /ustalibal H

= ' = name: Restart Nginx | VY Real-world Use Cases

; cervian |

} name: nginx | © Deploy web applications automatically

= i OE Se
~ : ” oe eae on meeaee mone $ Eieirenmentcaskey (Dis QA Z:Fred)

---

<!-- Source PDF page 16 -->

—_ Ansible Best Practi
—eeeeE—EEeeeeEoo_o0W03035~5~ ee,
Following Best Practices in Ansible helps in writing clean, secure,
efficient, reusable and maintainable automation.
|
@) Idempotency @ Folder Structure fj
* Ansible should produce the same result project/
(G) every time a Playbook is run. inventory / # Inventory files
* Use modules that are idempotent group_vars / # Group variables
(e.g, copy, yum, user). host_vars / # Host variables
* Avoid using command or shell unless roles / # Reusable components
necessary. websenven/
c ' handlers/ § # Handlers
@ Naming Conventions ae, See
: vars / # Role variables
* Use lowercase and underscores. | defaults / # Default variables
* Playbooks: site.yml, websexver. yml 1 meta / # Role dependencies
¢ Roles: webserver, database playbooks / # Playbooks
* Variables: user_name, db_ password files/ # Static files
* Files/Templates: httpd.conf. j2 templates / # Global templates
) Security & | ©) Performance Tips 71 | (6) Reusable Playbooks ey
* Use Ansible Vault for sensitive data. | * Use SSH pipelining (pipelining = True). * Use Roles for reusability.
* Avoid hardeoding credentials. | * Limit facts gathering (gather_facts: no). | * Use variables for flexibility,
* Use become for privilege escalation | « Use async + poll for long tasks. | * Use includes /imports for
instead of root login. | © Run tasks in parallel (forks). | modular playbooks.
* Restrict SSH access using keys. | * Use tags to run only required tasks. | * Use templates for dynamic files.
(a we :
@) Project Organization soy | A Common Mistakes
, |X Not using Idempotent modules
+ Keep everything xgeined, | _ Environment || x Hardending vrais
+ Separate environments. | Ej dev aa |X Poor foley structure
* Use meaningful EB test Ansible |) « pajtots || | | x nek tating rghecte
directories. B stesing Project * Variables |] | —
* Maintain README.md =o — | | YY Golden Rule
iil * Files | | Automate smartly, keep it
- on — ——— | | simple, secure ply ®

---

<!-- Source PDF page 17 -->

ie
" e e
. Common Interview Questions
OO
= '
2) @. What is Ansible? | @ Q@. Why is Ansible Agentless?
: —————— |
UND. <6; Ansa: San qpenanuee Aulonclion Gel > Ansible uses SSH (or WinRM for Windows)
., used for Configuration Management, Application to connect to managed nodes.
. Deployment, Task Automation, and Orchestration. No software/agent needs to be installed
= any agent on target machines. | lightweight, secure and easy to manage. No Agent!
|
a, -— te ii i ma ain ae ig i Ema
GQ. What is the diffrence bebveen —Innntry | (Zh) Q. What is the difference bubueen Modal
= Inventory and Playbook? | Module and Role?
yg > Inventory: A file that lists all the Hosts | > Module: A single unit of code that performs
* and Groups on which Ansible will run. v | a specific task (e.g., copy, service, user). Role
pg? Playbook: A YAML file that defines Tasks, in oy > Role: A way to organize playbooks and a)
eae. eais Seta | related files (tasks, vars, templates, handlers)
= on 0 in a structured directory.
~>~----------- == === == == == 555-55 555-5
be (5) . Difference between command and shell? gam | © Q. difference betunen Variables and Facts? Variables
Supports shell features like pipes (1), redirects (>) oe hone enmngeed: enay antag gation: fiics gal
‘ f ‘ Flexible (hardware, OS, IP, ete.). Stored in ansible_facts.
|
PM (7) Q. What is Ansible Vault? | 2. What are Handlers in Ane? ae
data such as passwords, API keys, tokens when notified by another task,
= stored in plain text. Handlers run once at the end of the Play.
3 | 7 :
MS Concepl [What to? [Key Pod) 9 Focus om rutuerld empl
9, tnt WA. cre
eS ams
x Module Si task unit __Parforms specific action | = A Explain with use cases for better
| Role _—_—'|_—Structured collection Reusability & scalabili \___ impression.
: de}
Auto discovered data [QS seat Spe eel, Ayn |

---

<!-- Source PDF page 18 -->

mo? Mistakes & Troublesh ting
ommon lS es ro ootin
be
= YAML Indentation Errors S Inventory Issues :
@) YAML Indentation Errors YAML @ = w=
= YAML is space~sensitive. — Wrong inventory leads to unreachable hosts w—
Correct
tasks: tasks: © Wrong IP /Hostname © Verify IP/hostname & connectivity
~ = tae eh oe © Host not added to group © Check group & host names
= nama: nginx name: nginx © Wrong group name © Use ansible-inventory --list
. ig aia oe Eeed © Inventory file not loaded © Use =t <inwentery> properly
= pam, asec ft oe + Duplicate hosts © Keep inventory clean & updated
Lad @ SSH Problems os KO) Permission Errors
~ SSH not Stet 55 « .
~ . service running ° service « No access © Use become:
* Wrong username © Use correct user (-u user) - ae ea © Fix file /dir Ste Cabin
Page | ° Wrong /private hey © Provide correct hey (i hey.pom) Tneufficiant file permissions perenne
my | + SSH port blocked © Open port 22 in firewall * Unable to write to directory | © Ensure writable paths
= © Host key verification failed © Run ssh=keyscan <host> * SSH key permission too open| © chmod 600 on private key
eee Pe eo |
beg Hard to find which variable ie used. Fever season (p>
7 Problem Precedence (high + low) Mista bi
: Soa yee Steet Ee ak Falta Using command instead of module “bine et tye
a ‘ > vars bal . . min
= | S| + Ung a chan mde ite | + Mods ome tegen
extra-vars, ete.) 5. Play vars © Using apt on RHEL / yum on Debian| © Choose OS specific modules
bec’ Confusion about final value. | 6. Inventory Chost_vars, group..vars) © Not using idempotent modules * Check module docs (ansible~doc)
7. Role defaults
= i —— ———
™ @) General Tr ing Ty : ® Quick Troubleshooting Commands
’ v Run playbook verbose moda: ansible~playbook -v or -vw 1
mY Syntax ag enitila=plaglek<aegulen-diesk <file.gnl> | (__Purpose | Command
SF Check invertery: ensitle=tewenlery ~—list |_Chack connectivity [ansible all -m ping
Pv Ping test: ansible all -m ping
¥ Limit hosts: --limit <host/group> | Syntax check [ansible playbook -~syntax-check site.yml |
Cheater, facet Attensa ce eee
ee A gin are patsy ecution | ansible-playbook site yml -—limit web |
y fn Fedde oak nade 6 tren
ae Ae er eee
be LY Interview Tip | (cn Kee 9 0 oe
| Always read the error message carefully. 95% of issues — Beer ia ee
oe | ee tee a ore ee ee small, simple, idempotent end reusable Playbooks.
3 | indentation. }

---

<!-- Source PDF page 19 -->

= e ee
a Quick Revision Cheat Sheet
. ee ——e—e—eeeeeeeOeeeeOEOeeOeee ee
bee (1) Most Used Commands @® Playbook Structure (YAML)
(Command | ‘Pups \| ("| ~-Y
Ms eno mane: lage. Nan
” bal oy | aoeng bea) | Ee ie Hest
“i id Run ad-hoc command | vars: = Sd
ansible-playbook playbook. ymi Run a Playbook | ag value j _
& _ansible~inventory ~~list | List inventory in JSON || = nema: Task name Y=! List of
’ ansible-galaxy role install <rola> | Install Rela from | module_nama: | oe ea
= ansible-vault encrypt <file> Encrypt a file with Vault option: value |
: a 5, name: httpd i iis ond
Ca (default) | state: restarted |
= en eine
? webl ansible_host = 192.168.1.10 |
| sab2 ansible_host 192.168.1.11 | ‘C) Module Summary (Commonly Used) © Interview Revision Table
eS | dt. enaile_host192.168.1.20 a ee
J | tabard cal Oy en |p ery [cee pe | [Ae | tn we SL |
* ensllls seem eid “isan . pd Tarts & Groupe defriton
sensible _ssh_private_hey_fila=~/.sah/id_roa {fet ; ; ees:
[ SRE nen ce Serviee | corvien apron: | Menage ewe | | Rlagheah | INL: fle with tasks & handlers
ae VAML Formal | Bay ter | cap, template, fle | Menge fil & tnyaes| | Module | Single wit of cade to perform task
elt | —|—___— _ —| | Role Organize playbooks & tasks
ears rm... : || Facts Auto discovered system info
a fe ee a
ansible_host: 192.168.1.10 =! event Bete oe
ali soos Geet fo } | GB Archie | unarchive, archive | Extract / Create erchies| | Vault Encrypt sensitive data
= hosts: Environments | (3) Vault Commands Idempetency no unwanted changes
= | ge eee | [ entte-wett rae <file> = Enerypt «file
L wit ; z enciile-vaalt view <file> > View enrypled file &
—————-—r-:—-.—-—.,.””——s | ansible-vault edit <file> > Edit encrypted file
i | ansible-vault decrypt <file> => Decrypt « file
Lemsetete te Ser ote
Quick Playbook Example ’(R) Ker Thkeawas ti (i‘ét;!;#;#;”™WWDWUUUUUU
ria ®D_Sast Patt Sane | @ Key Takeowoys )
a arate | Eee | Remember! _ |,
0 i Y Use Inventory to manage Hosts & | -(@)-
a Or ame: Inkl Ny | | ‘'dacldeerare te Liliy ke owe | | Automation is “Ws
Wail it | as hp rab wpa | not about writing
= state: prasent Y Use Variables, Facts & Templates for dynamic automation | complex code,
paral lorie aud aaa Knee | — Play ie
x ! y ensures i ‘ runs 5
Se, Raat Myon | Retest sl
a ames ngine |
SE: -teoeiee
