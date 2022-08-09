### TIL: Today I Learned (Weekly Changelog)
My weekly journey log regarding interesting things that I saw

## Week 19/1401
###### 30/2022
* Using Config Connector for Google Cloud resource management => https://www.youtube.com/watch?v=3lAOr2XdAh4
* https://medium.com/google-cloud/setting-up-config-connector-with-terraform-helm-8ce2f45f48a4
* https://globalcloudplatforms.com/2021/08/28/saving-the-day-how-cloud-sql-makes-data-protection-easy/
* Getting Started with Security Command Center => https://www.youtube.com/playlist?list=PLIivdWyY5sqKd-Cu1HZ7v5RiYE8gVsM7P
* https://cloud.google.com/security-command-center/pricing?&_ga=2.182793250.-545711799.1658748326#standard_tier_pricing
* https://nights.bearblog.dev/how-to-stop-being-terminally-online/

## Week 18/1401
###### 29/2022
* https://jamesclear.com/stop-procrastinating-seinfeld-strategy
* https://brandon.invergo.net/news/2012-05-26-using-gnu-stow-to-manage-your-dotfiles.html
* 
```
name: Cloudflare deleting old deployments workflow

# Controls when the action will run. Workflow runs when manually triggered using the UI
# or API.
on:
  workflow_dispatch:
    # Inputs the workflow accepts.
    inputs:
      EXPIRATION_DAYS:
        description: 'Deployments older than this amount of days should be deleted:'
        default: '10'
        required: true
env:
  ACCOUNT_ID: <ACCOUNT-ID>
  PROJECT_NAME: <PROJECT-NAME>
  API_KEY: ${{ secrets.API_KEY }}
  EXPIRATION_DAYS: ${{ github.event.inputs.EXPIRATION_DAYS }}

jobs:
  Check-and-delete-old-Deployments:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Check and delete old deployments
      run: |
            chmod +x "${GITHUB_WORKSPACE}/.github/scripts/cloudflare_cleanup_past_deployments.sh"
            "${GITHUB_WORKSPACE}/.github/scripts/cloudflare_cleanup_past_deployments.sh"
```
```
#!/usr/bin/env bash

# In this script we are going to get the list of Cloudflare project deployments and then delete the old ones.
# API_KEY , ACCOUNT_ID and PROJECT_NAME are mandatory environment variables.
# Also you can define EXPIRATION_DAYS but it is optional and the default value is 7.
# Technical API reference: https://developers.cloudflare.com/pages/platform/api/#deleting-old-deployments-after-a-week

set -o errexit
set -o pipefail

expiration="${EXPIRATION_DAYS:-7}"

function get_page_deployments_list_and_send_them_to_check_and_delete_old_deployments()
{
  curl --silent --show-error --header "content-type: application/json;charset=UTF-8" --header "Authorization: Bearer ${API_KEY}" -X GET "https://api.cloudflare.com/client/v4/accounts/${ACCOUNT_ID}/pages/projects/${PROJECT_NAME}/deployments" | jq -r '.result[]| .id + " " + .created_on' | while read args; do check_and_delete_old_deployments $args; done
}

function check_and_delete_old_deployments()
{
  local deployment_id="$1"
  local deployment_date="$2"

  time_ago="${expiration} days ago"
  deployment_time_sec=$(date --date "$deployment_date" +'%s')
  time_ago_sec=$(date --date "$time_ago" +'%s')
  if [ $deployment_time_sec -lt $time_ago_sec ]
  then
    echo "This deployment is going to be deleted:"
    curl --silent --show-error --header "content-type: application/json;charset=UTF-8" --header "Authorization: Bearer ${API_KEY}" -X DELETE "https://api.cloudflare.com/client/v4/accounts/${ACCOUNT_ID}/pages/projects/${PROJECT_NAME}/deployments/${deployment_id}"
  else
    echo "This deployment is still newer than ${expiration} days:"
  fi
  echo -n $deployment_id
  echo -n " "
  echo $deployment_date
}

function main
{
  [[ -z "$API_KEY" ]] && { echo "Error: API_KEY is empty"; exit 1; }
  [[ -z "$ACCOUNT_ID" ]] && { echo "Error: ACCOUNT_ID is empty"; exit 1; }
  [[ -z "$PROJECT_NAME" ]] && { echo "Error: PROJECT_NAME is empty"; exit 1; }

  get_page_deployments_list_and_send_them_to_check_and_delete_old_deployments;
}

main
```

## Week 14/1401 - 17/1401
###### 25/2022 - 28/2022
* [[[vacation]]]

## Week 13/1401
###### 24/2022
* `cat ~/.kube/config | yq e '.users.[].name' - | grep -v "master" | grep -v "bonial-eu" | while read ARGS; do echo $ARGS; kubectx $ARGS; kubectl get pod -A -o json | jq '.items[].spec.containers[].image' -r | sort -u | grep "library"; done `
* `cat ~/.kube/config | yq e '.users.[] | .user.exec.env[0].value + " " + .name' - | while read ARGS; do $(echo $ARGS|awk {'print "aws-azure-login --no-prompt --profile ",$1, "; kubectx ",$2,";" '}); done`
* `kubectl get --raw /apis/admissionregistration.k8s.io/v1/validatingwebhookconfigurations`
* `k get ValidatingWebhookConfiguration -A`
* `k get Mutatingwebhookconfiguration -A`

## Week 12/1401
###### 23/2022
* `git commit --allow-empty`
* `puppet agent -t --noop`
* `edit-and-execute-command (C-xC-e)
          Invoke  an  editor  on the current command line, and execute the
          result as shell commands.   Bash  attempts  to  invoke  $VISUAL,
          $EDITOR, and emacs as the editor, in that order.`
* https://learnvimscriptthehardway.stevelosh.com/chapters/08.html
* https://www.youtube.com/watch?v=ouZrZa5pLXk => ADD/ADHD | What Is Attention Deficit Hyperactivity Disorder?
* https://www.youtube.com/watch?v=RMaCE5RT54c => ADHD - What is it and what's the difference with ADD?
* https://www.youtube.com/watch?v=O0-9Zh6PZaM => 5 Signs of Inattentive ADHD (ADD)
* https://www.youtube.com/watch?v=hwJusJQUSHM => My Life with ADD - Rick Green
* https://www.youtube.com/watch?v=8cvhwquPqJ0 => BECOMING SUPERHUMAN WITH ICE MAN - Wim Hof
* https://www.youtube.com/watch?v=LNHBMFCzznE => After watching this, your brain will not be the same

## Week 11/1401
###### 22/2022
* `kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes"`
* `kubectl get pods -A -o jsonpath="{range .items[*]}{.metadata.annotations}{'\n'}{.metadata.name}{'\n'}{end}"`
* `git diff --cached`
* `ls --color=always | les`
* https://kubernetes.io/blog/2021/08/04/kubernetes-1-22-release-announcement/
* https://kubernetes.github.io/ingress-nginx/#what-is-an-ingressclass-and-why-is-it-important-for-users-of-ingress-nginx-controller-now
* https://www.ibm.com/docs/en/spp/10.1.8?topic=prerequisites-kubernetes-verifying-whether-metrics-server-is-running
* https://www.cloudskillsboost.google/course_sessions/1123143/video/268025
* https://content.fme.de/en/blog/docker-is-dead-podman-an-alternative-tool

## Week 10/1401
###### 21/2022
* `kubectl get ingress -A -o jsonpath="{range .items[*]}{.metadata.name} {.metadata.namespace}{'\n'}{end}" | grep -v "current context"| while read ARGS; do $(echo $ARGS|awk {'print "kubectl patch ingress -n",$2, $1, "--patch-file patch.yaml" '}); done`
* `kubectl patch ingress prometheus-operator-alertmanager  --type=json -p='[{"op": "remove", "path": "/spec/ingressClassName"}]' -n monitoring`
* https://kubernetes.io/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/
* https://kubernetes.io/docs/reference/kubectl/jsonpath/
* https://fzero.rubi.gd/post/general/gpg-step-by-step/
* https://cloud.google.com/kubernetes-engine/pricing
* https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-architecture

## Week 09/1401
###### 20/2022
* `k get endpoints -A -o wide`
* `terraform state list | while read ARGS;do echo $ARGS;terraform state rm $ARGS; done`
* https://blog.samaltman.com/how-to-be-successful
* Change Your Brain: Neuroscientist Dr. Andrew Huberman | Rich Roll Podcast => https://www.youtube.com/watch?v=SwQhKFMxmDY
* https://medium.com/swlh/kubernetes-ingress-controller-overview-81abbaca19ec
* https://www.cncf.io/blog/2020/08/26/why-do-devops-engineers-love-helm/
* https://kubernetes.io/blog/2021/07/14/upcoming-changes-in-kubernetes-1-22/
* https://kubernetes.io/docs/reference/using-api/deprecation-guide/#v1-22
* https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html
* https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/
* https://kubernetes.io/docs/reference/kubectl/jsonpath/
* https://www.aquasec.com/cloud-native-academy/devsecops/shift-left-devops/
* https://farokhnotes.medium.com/startup-launch-checklist-how-to-get-the-first-100-users-a7233b4d49a1

## Week 08/1401
###### 19/2022
* https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html
* https://kubernetes.io/blog/2021/07/14/upcoming-changes-in-kubernetes-1-22
* https://ervinbarta.com/2021/10/19/slo-alerting-for-mortals/
* Alerting on error budget burn rate => https://www.youtube.com/watch?v=t1BGo-Il1AM
* SLIs, SLOs, SLAs, oh my! (class SRE implements DevOps) => https://www.youtube.com/watch?v=tEylFyxbDLE
* How to build a PromQL (Prometheus Query Language) => https://www.youtube.com/watch?v=hvACEDjHQZE
* https://ypereirareis.github.io/blog/2020/02/21/how-to-join-prometheus-metrics-by-label-with-promql/ 
* How to learn Kubernetes in 2022 => https://www.youtube.com/watch?v=JeAHlTYB1Qk
* Kubernetes Ingress: NGINX Explained => https://www.youtube.com/watch?v=u948CURLDJA
* Don't Buy Just a Hammer Drill => https://www.youtube.com/watch?v=iwt-olc4nso
* https://foundation.mozilla.org/en/blog/top-mental-health-and-prayer-apps-fail-spectacularly-at-privacy-security/

## Week 07/1401
###### 18/2022
* To remove an specific line from all files => `sed -i '' '/registryProject: private/d' *`
* `git rev-list --all | git grep -i "imagepullpolicy"`
* SAML Explained in Plain English => https://www.onelogin.com/learn/saml#:~:text=SAML%20is%20an%20acronym%20used,one%20set%20of%20login%20credentials
* An Illustrated Guide to OAuth and OpenID Connect => https://www.youtube.com/watch?v=t18YB3xDfXI
* THE Guide for securing your K8s cluster => https://www.youtube.com/watch?v=oBf5lrmquYI&t=951s
* Introduction To LDAP - Common Terminologies => https://www.youtube.com/watch?v=NcvIqK4G_fQ
* https://www.stackrox.io/blog/kubernetes-networking-demystified/

## Week 06/1401
###### 17/2022
* How to Deploy a Kubernetes Cluster from Scratch => https://www.youtube.com/watch?v=t4H6hdvB9iQ
* What Is Kubernetes Node Affinity? => https://www.youtube.com/watch?v=6ZHjqpn9dck
* Kubernetes Explained => https://www.youtube.com/watch?v=aSrqRSk43lY
* How does Kubernetes create a Pod? => https://www.youtube.com/watch?v=BgrQ16r84pM
* https://developer.chrome.com/docs/extensions/
* https://developer.chrome.com/docs/extensions/reference/runtime/#event-onStartup
* https://developer.chrome.com/docs/extensions/mv3/service_workers/
* https://developer.chrome.com/docs/extensions/reference/alarms/#type-AlarmCreateInfo
* https://stackoverflow.com/questions/26883915/google-chrome-extension-alarm-at-specific-time
* https://github.com/pesarkhobeee/Harold

## Week 05/1401
###### 16/2022
* `kubectl create job --from=cronjob/<name of cronjob> <name of job>`
* https://ruslanspivak.com/lsbaws-part3
* https://buttondown.email/hillelwayne/archive/why-i-still-use-vim/
* https://thepugautomatic.com/2014/03/vims-life-changing-c-percent/
* 9 Steps to Awesome with Kubernetes by Burr Sutter: https://www.youtube.com/watch?v=ZpbXSdzp_vo
* https://github.com/burrsutter/9stepsawesome

## Week 04/1401
###### 15/2022
* `kubectl get events --sort-by='.metadata.creationTimestamp' -A`
* https://linuxhint.com/sort-kubectl-events-by-time/
* https://linuxhint.com/tag/kubernetes/
* https://castbox.fm/episode/%D8%A8%D8%B1%D9%86%D8%A7%D9%85%D9%87-%D9%86%D9%88%DB%8C%D8%B3%DB%8C-%D8%B3%DB%8C%D8%B3%D8%AA%D9%85%DB%8C-%D9%88-%D8%B3%D8%B7%D8%AD-%D9%BE%D8%A7%DB%8C%DB%8C%D9%86-%D8%A8%D8%A7-%DB%8C%D8%A7%D8%B3%D8%B1-%DA%98%DB%8C%D8%A7%D9%86-id4753594-id483693042?country=us
* https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Drawing_shapes
* https://coderwall.com/p/wohavg/creating-a-simple-tcp-server-in-go
* https://medium.com/bb-tutorials-and-thoughts/kubernetes-learn-sidecar-container-pattern-6d8c21f873d
* https://dynobase.dev/dynamodb-vs-aurora/#:~:text=Shared%20Attributes%20for%20DynamoDB%20and%20Aurora&text=And%2C%20the%20most%20significant%20difference,seamlessly%20scales%20up%20or%20down
* https://www.lastweekinaws.com/blog/aurora-vs-rds-an-engineers-guide-to-choosing-a-database/
* https://en.wiktionary.org/wiki/PEBCAK
* https://ruslanspivak.com/lsbaws-part1/
* https://ruslanspivak.com/lsbaws-part2/

## Week 03/1401
###### 14/2022

* core api (without namespace, e.g. pods): `kubectl get --raw /api/v1/pods | jq`
* namespaced apis: `kubectl get --raw /apis/networking.k8s.io/v1 | jq`
* `kubectl api-resources -o wide`
* `kubectl explain IngressClass`
* `kubectl explain IngressClass.spec --recursive`
* `kubectl get node -L node.kubernetes.io/instance-type -L spotinst.io/node-lifecycle`
* `kubectl delete --cascade=orphan statefulsets/harbor2-redis -n sdlc-ops`
* https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#ingressclass-v1-networking-k8s-io
* https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-class
* https://yourbasic.org/golang/fmt-printf-reference-cheat-sheet/#cheat-sheet
* https://stackoverflow.com/questions/19109912/yaml-do-i-need-quotes-for-strings-in-yaml
* https://aimlessengineer.com/2021/08/19/vpc-peering-vs-transit-gateway/#:~:text=Transit%20Gateway%20solves%20the%20complexity,Lower%20cost
* https://scalefactory.com/blog/2022/01/27/how-infrastructure-as-code-should-feel/
* http://www.designcult.org/2011/08/why-do-we-call-in-logging-in.html


## Week 02/1401
###### 13/2022

* `git worktree add --detach /Users/farid.ahmadian/works/terraform2 c07c9aaa0aa50fbe7598580ec631024f93839f74`
* What is a TLS Cipher Suite? https://www.youtube.com/watch?v=ZM3tXhPV8v0
* Technology Radar: https://www.thoughtworks.com/radar
* I don't want to learn your garbage query language: https://erikbern.com/2018/08/30/i-dont-want-to-learn-your-garbage-query-language.html
* Lessons in debugging: observe how programs interact with the Linux: https://johnysswlab.com/lessons-in-debugging-observe-how-programs-interact-with-the-linux-kernel-with-strace/
* https://grafana.com/blog/2022/03/30/announcing-grafana-mimir/

## Week 01/1401
###### 12/2022

* `kubectl get pod -A -o json | jq '.items[].spec.containers[].image' -r | sort -u`
* `git status -s | grep "\.\."| cut -f3 -d " " - | while read ARGS; do echo $ARGS; git checkout -- $ARGS; done`
* `date +"So this is week %U of the year"`
* https://jovica.org/posts/advanced_learner/
* https://vim.fandom.com/wiki/Category:VimTip
* https://github.com/charlax/professional-programming
* Kubernetes CNI vs Kube-proxy: 
https://stackoverflow.com/questions/53534553/kubernetes-cni-vs-kube-proxy
* How to Survive in Gamedev for Eleven Years Without a Hit: https://www.youtube.com/watch?v=JmwbYl6f11c
* Game Studio Leadership: You Can Do It: https://youtu.be/O1zP6yJjc1o
* Crafting A Tiny Open World: A Short Hike Postmortem: https://www.youtube.com/watch?v=ZW8gWgpptI8
