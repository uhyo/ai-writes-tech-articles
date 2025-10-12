# AI Writes Tech Articles

The ultimate goal of this project is to have an AI (LLM) write technical articles that are indistinguishable from those written by humans. This repository defines an iterative process to improve the quality of AI-generated articles through continuous feedback and refinement. This repository also serves as a workspace for the iterative process.

## Iterative Process

This repository contains a style guide (`style_guide.md`) that outlines the desired structure, tone, and formatting for the articles. Through the iterative process defined below, the style guide will be refined to ensure that the AI-generated articles meet the desired quality standards.

1. **Initial Article Generation**: An AI agent generates a technical article based on a given topic and the initial style guide.
2. **Article Review**: The generated article is reviewed by another, independent AI agent. The reviewer compares the generated article against the human-written articles in the `human-bench/articles` directory.
3. **Style Guide Refinement**: Based on the review, the style guide is updated to address any shortcomings or areas for improvement identified in the generated article.
4. **Iteration**: Steps 1-3 are repeated, with the AI agent generating new articles based on the updated style guide. This process continues until the generated articles are indistinguishable from human-written articles.
