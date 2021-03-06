# Longhorn

## Ingress Configuration

To run the Longhorn-UI frontend via traefik.io it is necessary to replace the hostname in the file 002.ingress.yaml.
Replace {YOUR-HOST-NAME} with your own Internet name:

	kind: Ingress
	apiVersion: networking.k8s.io/v1beta1
	metadata:
	  name: longhorn-ui
	  namespace: longhorn-system
	  annotations:
	    traefik.ingress.kubernetes.io/router.entrypoints: websecure
		traefik.ingress.kubernetes.io/router.middlewares: default-cors-all@kubernetescrd
	
	spec:
	  rules:
	  - host: {YOUR-HOST-NAME}
	    http:
	      paths:
	      - path: /
	        backend:
	          serviceName: longhorn-frontend
	          servicePort: 80

Activate a CORS middleware for the Ingress.

The CORS middleware is configured in the traefik object 004-middleware.yaml:

	---
	# Middleware for CORS
	apiVersion: traefik.containo.us/v1alpha1
	kind: Middleware
	metadata:
	  name: cors-all
	  namespace: default
	spec:
	  headers:
	    accessControlAllowMethods:
	      - "GET"
	      - "OPTIONS"
	      - "PUT"
	      - "POST"
	    accessControlAllowOrigin: "origin-list-or-null"
	    accessControlMaxAge: 100
	    accessControlAllowHeaders:
	      - "Content-Type"
	    addVaryHeader: "true"
    
## Deployment

To deploy Longhorn into your cluster run:

	$ kubectl apply -f management/longhorn/

The deployment may take some minutes. Corresponding to your ingress configuration you can open the Longhorn Web UI to administrate your cluster.
      