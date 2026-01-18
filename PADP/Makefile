.PHONY: help install test clean format lint

help: ## 显示帮助信息
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'

install: ## 安装依赖
	pip install -r requirements.txt
	pip install -e .

test: ## 运行测试
	pytest tests/ -v

test-cov: ## 运行测试并生成覆盖率报告
	pytest tests/ --cov=src --cov-report=html --cov-report=term

format: ## 格式化代码
	ruff format src/ tests/
	ruff check --fix src/ tests/

lint: ## 代码检查
	ruff check src/ tests/
	mypy src/

clean: ## 清理临时文件
	find . -type d -name __pycache__ -exec rm -r {} +
	find . -type f -name "*.pyc" -delete
	find . -type d -name "*.egg-info" -exec rm -r {} +
	rm -rf build/ dist/ .pytest_cache/ .coverage htmlcov/

.DEFAULT_GOAL := help
