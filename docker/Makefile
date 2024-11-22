#this file is used only to speed up local testing
NAME_IMAGE=geobuyer_api-php-fpm

#create an image in the local repository
create_image_php-fpm:
	docker build -t ${NAME_IMAGE}:latest -f ./php-fpm/Dockerfile ../

#run the image
run_php-fpm:
	docker run -d --name ${NAME_IMAGE} -p 9000:9000 ${NAME_IMAGE}:latest

#enter the image bash
in_php_fpm:
	docker exec -it ${NAME_IMAGE} /bin/bash

#stop and delete from the local repository
delete_php-fpm:
	docker stop ${NAME_IMAGE}  || true
	docker rm ${NAME_IMAGE} || true
	docker rmi ${NAME_IMAGE}:latest || true
