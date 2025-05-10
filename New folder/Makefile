include .env
OUT=bin
DB_URL=postgres://${DB_USERNAME}:${DB_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_DATABASE}

all: clean test build

clean:
	@echo "Cleaning..."
	@rm -fv ${OUT}/*

build:
	@echo "Building..."
	@go build -o ${OUT}/ ./cmd/api

run: build docker-start migrate-up
	@if command -v air > /dev/null; then \
		air -build.bin ./bin/api -build.cmd "make build"; \
	else \
		./${OUT}/api; \
	fi

.PHONY: test
test:
	@echo "Testing..."
	@go test ./... -v

.PHONY: itest
itest:
	@echo "Testing database integration..."
	@go test ./internal/database -v

.PHONY: docker-start
docker-start:
	@docker compose up --wait

.PHONY: docker-stop
docker-stop:
	@docker compose down

.PHONY: migrate-up
migrate-up:
	@goose -dir internal/database/migrations ${DB_DRIVER} ${DB_URL} up

.PHONY: migrate-down
migrate-down:
	@goose -dir internal/database/migrations ${DB_DRIVER} ${DB_URL} down

.PHONY: migrate-reset
migrate-reset:
	@goose -dir internal/database/migrations ${DB_DRIVER} ${DB_URL} reset

.PHONY: docs
docs:
	@swag init -g ./api/main.go -d cmd,internal && swag fmt

.PHONY: seed
seed:
	@go run ./cmd/seed
