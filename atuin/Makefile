include .env

.PHONY: up down backup restore update

up:
	docker compose up -d

down:
	docker compose down

logs:
	docker compose logs -f

backup:
	docker compose exec -T database pg_dump -Fc -U $(DATABASE_USER) $(DATABASE_NAME) > ~/backup/$(DATABASE_NAME)-$$(date +%F-%H-%M-%S).dump

restore:
ifndef FILE
	$(error Usage: make restore FILE=~/backup/db.dump)
endif
	docker compose up -d --wait database
	docker compose cp $(FILE) database:/tmp/restore.dump
	docker compose exec -T database pg_restore -Fc -U $(DATABASE_USER) -d $(DATABASE_NAME) /tmp/restore.dump
	docker compose exec -T database rm /tmp/restore.dump
	@echo "Restore complete."

update:
	docker run --rm -it -u $$(id -u):$$(id -g) -v $$(pwd):/cwd -w /cwd ghcr.io/updatecli/updatecli:latest apply
