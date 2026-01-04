# Requirements Document

## Introduction

This document specifies the requirements for recreating a personal website using Zola with the PaperMod theme, deploying it to GitHub, and hosting it at waifod.dev domain.

## Glossary

- **Zola**: Static site generator written in Rust
- **PaperMod**: A clean, responsive theme (originally for Hugo, needs Zola port)
- **Site**: The complete website including all pages, posts, and assets
- **Content**: Blog posts and pages written in Markdown
- **GitHub_Pages**: GitHub's static site hosting service
- **Domain**: The waifod.dev domain name

## Requirements

### Requirement 1: Zola Site Setup

**User Story:** As a website owner, I want to set up a new Zola site with a PaperMod-style theme, so that I have a modern, fast static website framework.

#### Acceptance Criteria

1. THE Site SHALL be initialized as a Zola project in the current directory
2. THE Site SHALL use a PaperMod-style theme (either ported or similar alternative)
3. THE Site SHALL have search functionality disabled in the configuration
4. THE Site SHALL include a configuration file with site metadata (title, author, base URL for waifod.dev)
5. THE Site SHALL support syntax highlighting for code blocks

### Requirement 2: Content Migration

**User Story:** As a website owner, I want to migrate all existing blog posts from the Zola site, so that no content is lost during the migration.

#### Acceptance Criteria

1. WHEN migrating content, THE Site SHALL preserve all three existing blog posts with their original dates
2. WHEN migrating content, THE Site SHALL convert Zola front matter format to Hugo front matter format
3. WHEN migrating content, THE Site SHALL preserve all markdown content including headings, quotes, and links
4. WHEN migrating content, THE Site SHALL maintain the "lain" tag for all posts
5. THE Site SHALL include a home page with author bio and contact information

### Requirement 3: Navigation Structure

**User Story:** As a website visitor, I want to navigate between home and blog sections, so that I can easily find content.

#### Acceptance Criteria

1. THE Site SHALL provide navigation to home page
2. THE Site SHALL provide navigation to blog listing page
3. THE Site SHALL display blog posts in reverse chronological order (newest first)
4. WHEN viewing the blog listing, THE Site SHALL show post titles, dates, and summaries

### Requirement 4: Git Repository Setup

**User Story:** As a website owner, I want the site in a Git repository, so that I can version control my content and deploy to GitHub.

#### Acceptance Criteria

1. THE Site SHALL be initialized as a Git repository
2. THE Site SHALL include a .gitignore file appropriate for Zola projects
3. THE Site SHALL have an initial commit with all site files
4. THE Site SHALL be configured to push to a GitHub remote repository

### Requirement 5: GitHub Deployment Configuration

**User Story:** As a website owner, I want the site configured for GitHub Pages deployment, so that it can be hosted publicly.

#### Acceptance Criteria

1. THE Site SHALL include a GitHub Actions workflow for automated deployment
2. WHEN code is pushed to the main branch, THE Site SHALL automatically build and deploy
3. THE Site SHALL be configured to deploy to GitHub Pages
4. THE Site SHALL use the waifod.dev custom domain in its configuration

### Requirement 6: Theme Configuration

**User Story:** As a website owner, I want the PaperMod theme properly configured, so that the site has a clean, professional appearance.

#### Acceptance Criteria

1. THE Site SHALL disable the search feature in PaperMod configuration
2. THE Site SHALL configure site metadata (title, description, author)
3. THE Site SHALL enable syntax highlighting for code blocks
4. THE Site SHALL configure navigation menu items
5. THE Site SHALL support social media links (GitHub, LinkedIn, Email)
