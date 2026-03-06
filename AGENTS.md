# AGENTS.md

This document provides guidelines for AI coding agents working in this repository.

## Project Overview

This is a Python FastAPI backend application for a travel assistant (Ctrip-style) that uses LangGraph and LangChain for AI-powered workflow automation. The application handles flight bookings, hotel reservations, car rentals, and trip recommendations.

## Build/Lint/Test Commands

### Running the Application
```bash
python main.py
```
Or using uvicorn directly:
```bash
uvicorn main:Server.app --host 127.0.0.1 --port 8000
```

### Installing Dependencies
```bash
pip install -r requirements.txt
```

### Linting (if ruff is available)
```bash
ruff check .
```

### Type Checking (if mypy is available)
```bash
mypy .
```

### Running Tests
No test suite is currently configured. If tests are added:
```bash
pytest
```
For a single test file:
```bash
pytest tests/test_file.py
```
For a single test function:
```bash
pytest tests/test_file.py::test_function_name -v
```

## Project Structure

```
ctrip_assistant/
├── main.py                 # Application entry point
├── config/                 # Configuration (Dynaconf)
│   ├── __init__.py        # Settings initialization
│   ├── development.yml    # Development config
│   ├── production.yml     # Production config
│   └── log_config.py      # Logging configuration
├── api/                    # FastAPI routes and schemas
│   ├── routers.py         # Main router initialization
│   ├── schemas.py         # Base schema types
│   ├── system_mgt/        # User management endpoints
│   └── graph_api/         # Graph workflow endpoints
├── db/                     # Database layer
│   ├── __init__.py        # SQLAlchemy engine and session
│   ├── dao.py             # Base DAO class
│   └── system_mgt/        # User models and DAOs
├── utils/                  # Utilities
│   ├── dependencies.py    # FastAPI dependencies
│   ├── jwt_utils.py       # JWT token handling
│   ├── password_hash.py   # Password hashing
│   ├── handler_error.py   # Exception handlers
│   ├── cors.py            # CORS configuration
│   └── middlewares.py     # Custom middlewares
├── graph_chat/             # LangGraph workflow definitions
│   ├── finally_graph.py   # Main workflow graph
│   ├── state.py           # Workflow state definitions
│   ├── assistant.py       # AI assistant nodes
│   └── build_child_graph.py  # Sub-workflow builders
└── tools/                  # LangChain tools
    ├── flights_tools.py   # Flight-related tools
    ├── hotels_tools.py    # Hotel-related tools
    ├── car_tools.py       # Car rental tools
    └── trip_tools.py      # Trip recommendation tools
```

## Code Style Guidelines

### Imports
- Group imports in the following order: standard library, third-party, local imports
- Use absolute imports from project root (e.g., `from config import settings`)
- Import FastAPI components from `fastapi` and Starlette components from `starlette`

```python
import os
from typing import List, Optional

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session

from config import settings
from db import sm
```

### Naming Conventions
- **Classes**: PascalCase (e.g., `UserModel`, `CtripAssistant`)
- **Functions/Methods**: snake_case (e.g., `get_user_by_username`, `init_app`)
- **Variables**: snake_case (e.g., `user_info`, `primary_assistant_tools`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `LOG_LEVEL`, `ACCESS_TOKEN_EXPIRE_MINUTES`)
- **Private module-level variables**: prefix with underscore (e.g., `_BASE_DIR`, `_dao`)
- **Database tables**: prefixed with `t_` (e.g., `t_usermodel` - auto-generated from model class)

### Type Annotations
- Always use type hints for function parameters and return types
- Use `Optional[T]` or `Union[T, None]` for nullable types
- Use `List[T]` for list types
- For SQLAlchemy models, use `Mapped[T]` with `mapped_column`

```python
def get_user_by_username(self, session: Session, username: str) -> UserModel:
    ...

def search_user(self, session: Session, uid: int = None) -> Query:
    ...
```

### Pydantic Schemas
- Name schemas with `Schema` suffix (e.g., `UserSchema`, `CreateOrUpdateUserSchema`)
- Use `BaseModel` as parent class
- Use `Field()` with description for all fields
- Inherit from `InDBMixin` for response schemas that map from ORM models

```python
class UserSchema(BaseUserSchema, InDBMixin):
    id: int = Field(description='User ID')
    create_time: Union[datetime, None] = Field(description='Creation time', default=None)
```

### SQLAlchemy Models
- Inherit from `DBModelBase` (defined in `db/__init__.py`)
- Use `Mapped[T]` type hints with `mapped_column()`
- Table names are auto-generated as `t_<class_name_lower>`

```python
class UserModel(DBModelBase):
    username: Mapped[str] = mapped_column(String(20), unique=True, nullable=False)
    email: Mapped[str] = mapped_column(String(50), nullable=True)
```

### FastAPI Routes
- Use `APIRouter()` for route grouping
- Include `description` and `summary` in route decorators
- Specify `response_model` for type-safe responses
- Use dependency injection for database sessions

```python
router = APIRouter()

@router.get('/users/', description='Get all users', response_model=List[UserSchema])
def get_users(session: Session = Depends(get_db)):
    return _dao.get(session)
```

### Error Handling
- Raise `HTTPException` from `starlette.exceptions` for HTTP errors
- Use appropriate status codes from `starlette.status`
- Register exception handlers in `utils/handler_error.py`

```python
from starlette.exceptions import HTTPException
from starlette import status

if not user:
    raise HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail='User not found'
    )
```

### Comments and Documentation
- Use Chinese comments for explanations (as per existing codebase convention)
- Use docstrings for classes and public methods
- Include parameter descriptions in docstrings

```python
def get_user_by_username(self, session: Session, username: str):
    """
    Query user object by username
    :param session: Database session
    :param username: Username to search
    :return: UserModel instance or None
    """
```

### LangGraph/LangChain Patterns
- Define state using `TypedDict` with `Annotated` types
- Use `add_messages` reducer for message lists
- Create runnable chains using `|` operator

```python
class State(TypedDict):
    messages: Annotated[list[AnyMessage], add_messages]
    user_info: str
```

### Configuration
- Use Dynaconf for configuration management
- Access settings via `from config import settings`
- Environment-specific configs in `development.yml` and `production.yml`

### Database Sessions
- Use dependency injection for session management
- Always close sessions in `finally` block

```python
def get_db(request: Request) -> Session:
    try:
        session = sm()
        yield session
    finally:
        session.close()
```

## Important Notes

- The application uses MySQL as the primary database (configured in `development.yml`)
- JWT authentication is implemented with OAuth2 password flow
- The AI workflow uses LangGraph with checkpoint memory for state persistence
- Sensitive operations in the workflow have `interrupt_before` for human approval