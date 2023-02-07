# helmPrivateRepo

#### Helm yaratishni o'rganamiz oddiy nginx uchun helm

Helm ni o'rganishdan oldin bizga kerakli narsalarni to'g'irlab olishimiz kerak.
+ k8s cluster
+ laptopimizga helm o'rnatilgan bo'lishi kerak. O'rnatish uchun 👉 [helm.sh](https://helm.sh/docs/intro/install/) ga bosing.

Birinchi navbatda tepadagi fayllarni to'g'irlab olamiz 
```
$ ls
nginx-chart-private  README.md
```
```
$ tree
```
👆bu buyruqda terminalda quyidagicha qilib ko'rishingiz mumkin, 👇tekshirish uchun!!!
```
$ tree
.
├── nginx-chart-private
│   ├── charts
│   ├── Chart.yaml
│   ├── templates
│   │   ├── configmap.yaml
│   │   ├── deployment.yaml
│   │   ├── secret.yaml
│   │   └── service.yaml
│   └── values.yaml
└── README.md

3 directories, 7 files
```
`Chart.yaml` faylimiz quyidagicha
```
apiVersion: v2
name: private-chart
description: My First Helm Chart
type: application
version: 0.1.0
appVersion: "1.2.0"
maintainers:
- email: abdulvoris000urolov@gmail.com
  name: abdulvoris
```
`values.yaml` faylimiz quyidagicha
```
replicaCount: 2

image:
  repository: [username/image:tag] #buyerga sizning dockerhubdagi private repo image yoziladi
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  name: private-service
  type: ClusterIP
  port: 80
  targetPort: 9000

env:
  name: dev
```

`configmap.yaml` faylimiz quyidagicha
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-index-html-configmap
  namespace: default
data:
  index.html: |
    <html>
    <h1>Welcome</h1>
    </br>
    <h1>Hi! I got deployed in {{ .Values.env.name }} Environment using Helm Chart </h1>
    </html
```
`deployment.yaml` faylimiz quyidagicha
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-private
  labels:
    app: private
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: private
  template:
    metadata:
      labels:
        app: private
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
      imagePullSecrets: 
        - name: private-chart
```
`service.yaml` faylimiz quyidagicha
```
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-service
spec:
  selector:
    app.kubernetes.io/instance: {{ .Release.Name }}
  type: {{ .Values.service.type }}
  ports:
    - protocol: {{ .Values.service.protocol | default "TCP" }}
      port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
```
`.helmignore` faylimiz quyidagicha
```
# Patterns to ignore when building packages.
# This supports shell glob matching, relative path matching, and
# negation (prefixed with !). Only one pattern per line.
.DS_Store
# Common VCS dirs
.git/
.gitignore
.bzr/
.bzrignore
.hg/
.hgignore
.svn/
# Common backup files
*.swp
*.bak
*.tmp
*.orig
*~
# Various IDEs
.project
.idea/
*.tmproj
.vscode/
```
`secret.yaml`  faylimiz quyidagicha
```
apiVersion: v1
kind: Secret
metadata:
  name: private-chart
  namespace: default
data:
  .dockerconfigjson: #(buyerda siz docker login qilasiz birinchi navbatda va `cat ~/.docker/config.json | base64 -w 0` buyruqni terminaldan terib chiqgan tokenni qoyasiz ushbu comment o'rniga) 
type: kubernetes.io/dockerconfigjson
```
va fayllarimiz to'g'ri yozilganligiga ishonch hosil qilish uchun quyidagi buyruqni yozamiz
```
helm lint nginx-chart nginx-chart
```
va siz qiymatlar almashayotganni ko'rish uchun quyidagicha tekshirib olsayiz bo'ladi
```
helm template nginx-chart nginx-chart
```
va hammasi tayyor bo'lib ishlab turgan bo'lsa unga 