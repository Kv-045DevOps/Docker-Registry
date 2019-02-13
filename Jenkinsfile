
def label = "mypod-${UUID.randomUUID().toString()}"
def dockerRegistry = "100.71.71.71:5000"
def Creds = "git_cred"

properties([
    parameters([
        stringParam(
            defaultValue: '***', 
            description: '', 
            name: 'service')
    ])
])

podTemplate(label: label, annotations: [podAnnotation(key: "sidecar.istio.io/inject", value: "false")], containers: [
  containerTemplate(name: 'python-alpine', image: 'ghostgoose33/python-alp:v3', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'helm', image: 'ghostgoose33/kubectl_helm:v1', command: 'cat', ttyEnabled: true)
],
volumes: [
  hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
], serviceAccount: "jenkins") 
{


node(label)
{
    try{
	stage("Pre-Test"){
            dir('get'){
            git(branch: "test", url: 'https://github.com/Kv-045DevOps/SRM-GET.git', credentialsId: "${Creds}")
            imageTagUI = (sh (script: "git rev-parse --short HEAD", returnStdout: true))
            tmp = "1"
            pathTocodeget = pwd()
            }
        }
        stage("Deploy to Kubernetes"){
            if(params.service == "db"){
		container ("helm"){
		    sh "helm upgrade --install --namespace production --force db-service ${pathTocodeget}/List-Helm-Charts/db-service --set=deploy.version=v1,conf.ver=v1,image.tag=${params.imageTagDB_}"
			
	    }
	    }
            if(params.service == "get"){
		container ("helm"){
		    sh "helm upgrade --install --namespace production --force get-service ${pathTocodeget}/List-Helm-Charts/get-service --set=deploy.version=v1,conf.ver=v1,image.tag=${params.imageTagGET_}"
			
	    }
	    }
	    if(params.service == "ui"){
		container ("helm"){
		    sh "helm upgrade --install --namespace production --force ui-service ${pathTocodeget}/List-Helm-Charts/ui-service --set=deploy.version=v1,conf.ver=v1,image.tag=${params.imageTagUI_}"
			
	    }
	    }
            
            if(params.service == "post"){
		container ("helm"){
		    sh "helm upgrade --install --namespace production --force post-service ${pathTocodeget}/List-Helm-Charts/post-service --set=deploy.version=v1,conf.ver=v1,image.tag=${params.imageTagPOST_}"
			
	    }
	    }    
        }

	sleep 10

        stage ("Prod Tests"){
            container('python-alpine'){
	            //sh "python3 /e2e-test-prod.py"
          }
        }
        
    }
    catch(err){
        currentBuild.result = 'Failure'
    }
}
}
