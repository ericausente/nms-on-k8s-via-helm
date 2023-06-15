helm install -n nms --set nms-hybrid.adminPasswordHash=$(openssl passwd -1 'YourPassword123#') nms nginx-stable/nms --create-namespace -f values.yaml --wait
