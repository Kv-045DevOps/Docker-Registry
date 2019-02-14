
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
		        check = sh(script: """helm list -c db-service | awk '{ print \$1 }' | grep v1""", returnStdout:true)
		        echo "${check}"
		        if (check){
		            sh "helm upgrade --install --namespace production --force db-service-v2 ${pathTocodeget}/List-Helm-Charts/db-service-bg --set=deploy.version=v2,image.tag=${params.imageTagDB_}"
                    sh "helm upgrade --install --namespace production --force istio-rules-db ${pathTocodeget}/List-Helm-Charts/istio-rules --set weight.v1=0,--set weight.v2=100, --set app=db-service"}            
                else{
                    sh "helm upgrade --install --namespace production --force db-service-v1 ${pathTocodeget}/List-Helm-Charts/db-service --set=deploy.version=v1,image.tag=${params.imageTagDB_}"
                }			
	    }
	    }
            if(params.service == "get"){
		container ("helm"){
		        check = sh(script: """helm list -c get-service | awk '{ print \$1 }' | grep get""", returnStdout:true)
		        echo "${check}"
		        if (check){
		            sh "helm upgrade --install --namespace production --force get-service-v2 ${pathTocodeget}/List-Helm-Charts/get-service-bg --set=deploy.version=v2,image.tag=${params.imageTagGET_}"
                    sh "helm upgrade --install --namespace production --force istio-rules-get ${pathTocodeget}/List-Helm-Charts/istio-rules --set weight.v1=0 --set weight.v2=100 --set app.name=get"}            
                else{
                    sh "helm upgrade --install --namespace production --force get-service-v1 ${pathTocodeget}/List-Helm-Charts/get-service --set=deploy.version=v1,image.tag=${params.imageTagGET_}"
                }			
	    }
	    }
	    if(params.service == "ui"){
		container ("helm"){
		        check = sh(script: """helm list -c ui-service | awk '{ print \$1 }' | grep v1""", returnStdout:true)
		        echo "${check}"
		        if (check){
		            sh "helm upgrade --install --namespace production --force ui-service-v2 ${pathTocodeget}/List-Helm-Charts/ui-service-bg --set=deploy.version=v2,image.tag=${params.imageTagUI_}"
                    sh "helm upgrade --install --namespace production --force istio-rules-ui ${pathTocodeget}/List-Helm-Charts/istio-rules --set weight.v1=0,--set weight.v2=100, --set app.name=ui"}            
                else{
                    sh "helm upgrade --install --namespace production --force ui-service-v1 ${pathTocodeget}/List-Helm-Charts/ui-service --set=deploy.version=v1,image.tag=${params.imageTagUI_}"
                }			
	    }
	    }
            
            if(params.service == "post"){
		container ("helm"){
		        check = sh(script: """helm list -c post-service | awk '{ print \$1 }' | grep v1""", returnStdout:true)
		        echo "${check}"
		        if (check){
		            sh "helm upgrade --install --namespace production --force post-service-v2 ${pathTocodeget}/List-Helm-Charts/post-service-bg --set=deploy.version=v2,image.tag=${params.imageTagPOST_}"
                    sh "helm upgrade --install --namespace production --force istio-rules-post ${pathTocodeget}/List-Helm-Charts/istio-rules --set weight.v1=0,--set weight.v2=100, --set app.name=post"}            
                else{
                    sh "helm upgrade --install --namespace production --force post-service-v1 ${pathTocodeget}/List-Helm-Charts/post-service --set=deploy.version=v1,image.tag=${params.imageTagPOST_}"
                }			
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
