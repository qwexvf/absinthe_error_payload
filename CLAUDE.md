# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AbsintheErrorPayload is an Elixir library that bridges the gap between Ecto and Absinthe GraphQL by listing validation messages in a mutation payload. It treats validation messages as data rather than errors, returning them in a structured format within GraphQL responses.

**Elixir Version**: This project requires Elixir ~> 1.18 and is compatible with OTP 25, 26, and 27.

## Common Development Commands

### Dependencies
- Install dependencies: `mix deps.get --force` or `make dependencies`

### Testing
- Run test suite: `mix test` or `make test`
- Run specific test: `mix test test/filename_test.exs:line_number`
- Generate coverage report: `mix coveralls` or `make test-coverage`

### Linting and Formatting
- Run all linting: `make lint` (includes compile warnings, format check, and credo)
- Check compilation warnings: `mix compile --warnings-as-errors --force`
- Check code formatting: `mix format --dry-run --check-formatted`
- Run Credo: `mix credo --strict`
- Auto-format code: `mix format` or `make format`

## Architecture Overview

The library consists of several key components that work together to convert Ecto changeset errors into structured GraphQL payloads:

1. **AbsintheErrorPayload.Payload** (lib/absinthe_error_payload/payload.ex)
   - Core middleware that transforms resolver outputs into standardized payload responses
   - Payloads contain three fields: `successful` (boolean), `messages` (list of validation errors), and `result` (the data object)

2. **AbsintheErrorPayload.ChangesetParser** (lib/absinthe_error_payload/changeset_parser.ex)
   - Converts Ecto changesets into ValidationMessage structs
   - Handles nested errors and maintains field hierarchy
   - Uses configurable field constructors for custom field naming

3. **AbsintheErrorPayload.ValidationMessage** (lib/absinthe_error_payload/validation_message.ex)
   - Struct representing individual validation errors
   - Contains: field, message, template, code, and options

4. **AbsintheErrorPayload.FieldConstructor** (lib/absinthe_error_payload/field_constructor.ex)
   - Default implementation for constructing field names
   - Can be overridden via application configuration

5. **AbsintheErrorPayload.ValidationMessageTypes** (lib/absinthe_error_payload/validation_message_types.ex)
   - Absinthe schema definitions for GraphQL types
   - Provides importable types for schema integration

## Key Integration Points

1. In your Absinthe schema:
   - Import the payload builder: `import AbsintheErrorPayload.Payload`
   - Import validation types: `import_types AbsintheErrorPayload.ValidationMessageTypes`
   - Create payload objects using: `payload_object(payload_name, object_name)`
   - Add middleware to mutations: `middleware &build_payload/2`

2. The field constructor can be customized by setting the application environment:
   ```elixir
   config :absinthe_error_payload, field_constructor: YourCustomFieldConstructor
   ```

## Testing Support

The library includes AbsintheErrorPayload.TestHelper with utilities for testing GraphQL responses in ExUnit tests.