IMAGE_TAG := "posestimate_cpu"

build:
	docker buildx build --platform=linux/amd64 -t ${IMAGE_TAG} -f Dockerfile ../

run:
	# Make sure directories exist to prevent permission issues
	docker run \
		--platform linux/amd64 \
		-e PYTHONUNBUFFERED=1 \
		-p 5000:5000 \
		--rm \
		-v `pwd`/../:/posestimate/ \
		-v ~/.aws/:/root/.aws/:ro\
		${IMAGE_TAG}

interactive:
	docker run \
		--platform linux/amd64 \
		-it \
		-e AWS_PROFILE=$${AWS_PROFILE:-testing} \
		-e PYTHONUNBUFFERED=1 \
		-p 5000:5000 \
		--rm \
		-e DISPLAY=${DISPLAY} \
		-v /tmp/.X11-unix:/tmp/.X11-unix \
		-v `pwd`/../:/posestimate/ \
		-v ~/.aws/:/root/.aws/:ro\
		${IMAGE_TAG} \
		/bin/bash
