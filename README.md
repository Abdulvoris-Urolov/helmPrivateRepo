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