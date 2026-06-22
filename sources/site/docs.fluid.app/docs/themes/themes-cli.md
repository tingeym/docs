# Source: https://docs.fluid.app/docs/themes/themes-cli

# Themes CLI Guide

- [Introduction](https://docs.fluid.app/#introduction)
- [Features](https://docs.fluid.app/#features)
- [Start Up Guide](https://docs.fluid.app/#start-up-guide)
- [FAQ](https://docs.fluid.app/#faq)

## Introduction

Themes CLI is an ongoing feature for the Themes and Builders product from Fluid. The goal of the feature is to provide on going support and tooling for developers looking to create themes on our platform. While allowing the use of AI agents and the developers prefered choice of IDE.

## Features

### Authentication Commands

#### Login

Log in to your Fluid account.

fluid login

This will prompt you to authenticate through admin.fluid.app.

#### Logout

Log out of your current Fluid session.

fluid logout

This will clear your authentication credentials and require you to log in again to use the CLI.

#### Whoami

Display the currently logged in company.

fluid whoami

### Company Management

#### Switch

Switch between different companies associated with your Fluid account.

fluid switch

The CLI will present a list of available companies for you to choose from.

**Options:**

- `-c <company_subdomain>` or `--company=<company_subdomain>` - Switch directly to a specific company without being prompted to choose from a list.

**Example:**

fluid switch \-c mystore
\# or
fluid switch \--company\=mystore

### Theme Management

The `fluid theme` command manages themes with several subcommands:

#### Theme Dev

Start a local development server for theme development.

fluid theme dev

> **Note:** Currently, `fluid theme dev` only works with gem installation. We are working on a fix for Homebrew installations.

#### Theme Init

Initialize a new theme project.

fluid theme init

#### Theme Pull

Pull an existing theme from Fluid to your local development environment.

fluid theme pull

**Options:**

- `--nodelete` - Preserve local files that don't exist on the server. By default, files that exist locally but not on the server will be deleted to match the server state.

**Example:**

fluid theme pull \--nodelete

#### Theme Push

Push your local theme changes to Fluid.

fluid theme push

**Options:**

- `--unpublished` - Create a new draft theme on the server instead of pushing to the existing theme. Use this when you want to create a new version without affecting the current published theme.
- `--nodelete` - Preserve files on the server that don't exist locally. By default, files that exist on the server but not locally will be deleted to match your local state.

**Examples:**

\# Push changes to existing theme
fluid theme push

\# Create a new draft theme
fluid theme push \--unpublished

\# Push without deleting server files
fluid theme push \--nodelete

\# Combine options
fluid theme push \--unpublished \--nodelete

### Future Development in Progress

We're constantly improving Themes CLI. Upcoming features and enhancements will be documented here as they become available.

## Start Up Guide

### Installation

The Fluid CLI is now available on RubyGems and Homebrew! Choose your preferred installation method:

#### Option 1: Install via RubyGems

gem install fluid\_cli

#### Option 2: Install via Homebrew

brew tap fluid-commerce/fluid
brew install fluid\_cli

Or alternatively:

brew install fluid-commerce/fluid/fluid\_cli

> **Important:** If you have a previous version of fluid\_cli installed (especially from the GitHub repo), make sure to uninstall it first to avoid conflicts.

### Authentication

Before using Themes CLI, you need to authenticate with your Fluid account:

fluid login

1. You'll be prompted to log in through admin.fluid.app
2. Once authenticated, you'll be automatically connected to your currently selected company

To check which company you're currently connected to:

fluid whoami

#### Switching Companies

If you need to work with a different company:

fluid switch

This will allow you to select from the companies associated with your account.

### Getting Started with Themes

1. Create a directory for your theme project:

mkdir my-theme
cd my-theme

2. Initialize a new theme (optional):

fluid theme init

3. Use the available theme commands:
 
 **Pull an existing theme from Fluid:**
 

fluid theme pull

**Start local development server:**

fluid theme dev

**Push your local theme changes to Fluid:**

fluid theme push

## Command Changes

> **Note:** The executable command has changed from `fluid_cli` to `fluid`. All commands now use the shorter `fluid` prefix for better usability.

## FAQ

Any questions please ask [kevin@fluid.app](mailto:kevin@fluid.app)