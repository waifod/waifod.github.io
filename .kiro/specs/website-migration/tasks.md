# Implementation Plan: Website Migration

## Overview

This plan outlines the step-by-step implementation of recreating a personal website using Zola with the PaperMod theme, deploying it to GitHub Pages, and configuring it for the waifod.dev domain.

## Tasks

- [x] 1. Initialize Zola project structure
  - Run `zola init` in current directory
  - Configure basic project settings
  - _Requirements: 1.1_

- [ ] 2. Install and configure PaperMod theme
  - [x] 2.1 Add PaperMod theme as git submodule
    - Execute `git submodule add https://github.com/cydave/zola-theme-papermod themes/papermod`
    - Initialize and update submodule
    - _Requirements: 1.2_
  
  - [x] 2.2 Configure theme in config.toml
    - Set `theme = "papermod"`
    - Configure `[extra.papermod]` section with `enable_search = false`
    - Set base_url to "https://waifod.dev"
    - Configure site metadata (title, author)
    - Enable syntax highlighting
    - _Requirements: 1.3, 1.4, 1.5, 6.1, 6.2, 6.3, 6.4_

- [ ] 3. Migrate content from existing site
  - [x] 3.1 Create home page content
    - Create `content/_index.md` with author bio
    - Add introduction text
    - Include social links (GitHub, LinkedIn, Email, CV)
    - _Requirements: 2.5_
  
  - [x] 3.2 Set up blog structure
    - Create `content/blog/_index.md` with pagination and sorting config
    - _Requirements: 3.3, 3.4_
  
  - [x] 3.3 Migrate blog posts
    - Copy three blog posts from ~/learning/waifod.github.io/content/blog/
    - Ensure front matter format is correct (TOML with +++delimiters)
    - Preserve titles, dates, tags, and markdown content
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [x] 4. Configure navigation
  - Update config.toml with navigation menu items
  - Add home and blog navigation links
  - _Requirements: 3.1, 3.2, 6.4, 6.5_

- [x] 5. Set up static assets
  - Create `static/` directory if needed
  - Add any static files (favicon, CV PDF, etc.)
  - _Requirements: 2.5_

- [x] 6. Test local build
  - Run `zola build` to verify site builds successfully
  - Run `zola serve` to preview site locally
  - Verify search is disabled
  - Check navigation works correctly
  - Verify blog posts display in correct order
  - _Requirements: 1.1, 1.2, 1.3, 3.3, 6.1_

- [-] 7. Initialize Git repository
  - [x] 7.1 Create .gitignore file
    - Add `public/` and `.DS_Store` to .gitignore
    - _Requirements: 4.2_
  
  - [-] 7.2 Initialize git and make initial commit
    - Run `git init`
    - Add all files
    - Create initial commit with message "feat: initial Zola site with PaperMod theme"
    - _Requirements: 4.1, 4.3_

- [ ] 8. Configure GitHub Actions deployment
  - [ ] 8.1 Create workflow file
    - Create `.github/workflows/deploy.yml`
    - Configure workflow to trigger on push to main branch
    - Use `shalzz/zola-deploy-action` for build and deployment
    - _Requirements: 5.1, 5.2, 5.3_
  
  - [ ] 8.2 Configure GitHub repository settings
    - Document steps for user to create GitHub repository
    - Document steps to configure GitHub Pages (deploy from gh-pages branch)
    - Document steps to add custom domain waifod.dev in repository settings
    - _Requirements: 4.4, 5.3, 5.4_

- [ ] 9. Final verification
  - Verify all content migrated correctly
  - Check that search is disabled
  - Confirm navigation works
  - Ensure blog posts are in correct order
  - Review GitHub Actions workflow configuration
  - _Requirements: 1.3, 2.1, 2.2, 2.3, 2.4, 3.1, 3.2, 3.3, 6.1_

## Notes

- Tasks are designed to be executed sequentially
- Each task builds on previous tasks
- Testing is integrated throughout the process
- GitHub repository creation and DNS configuration require manual steps by the user
- The site will be ready for deployment once all tasks are complete
