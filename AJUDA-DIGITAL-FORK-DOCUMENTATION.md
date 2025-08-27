# AJUDA DIGITAL - Custom Open WebUI Implementation

## About This Implementation

This repository contains a customized implementation of Open WebUI, specifically adapted for AJUDA DIGITAL - a government services AI assistant for Timor-Leste. Our implementation extends the original platform with specialized features for multilingual government service delivery.

## Project Overview

AJUDA DIGITAL serves as a proof-of-concept for AI-powered government services in Timor-Leste, providing citizens with accessible information about constitutional law, administrative procedures, and government services in their native languages.

## Key Modifications

### Branding & Localization
- **Custom Visual Identity**: AJUDA DIGITAL branding, logos, and Timor-Leste cultural elements
- **Multilingual Support**: Enhanced support for Tetum, Portuguese, and English
- **Cultural Adaptation**: UI elements adapted for Southeast Asian government context

### Functional Enhancements
- **Department-Specific Models**: Specialized AI assistants for different government departments
- **Document Processing**: Optimized for government document formats and languages
- **User Interface**: Simplified navigation for citizens accessing government services
- **Demo Environment**: Test platform configuration for development and demonstration

### Technical Customizations
- **Authentication**: Custom login flows with demo credentials
- **Database Configuration**: Optimized for PostgreSQL with PGVector integration
- **API Integration**: Enhanced support for SEA-LION and AWS Bedrock models
- **Deployment**: Container-ready configuration for cloud deployment

## License Compliance & Attribution

This implementation operates under the Open WebUI license terms:

- **Current Status**: Deployed as demonstration platform (< 50 users)
- **License**: Open WebUI BSD 3-Clause License with branding provisions
- **Attribution**: Full credit maintained to original Open WebUI project
- **Compliance**: Operating within license exceptions for small-scale implementations

### Scaling Commitment
Upon reaching 50+ active users, we commit to:
1. Obtaining appropriate commercial licensing from Open WebUI
2. Establishing formal partnership agreements
3. Contributing improvements back to the upstream project
4. Maintaining full compliance with enterprise licensing terms

## Technical Architecture

### Base Configuration
- **Platform**: Open WebUI v0.6.25+
- **Deployment**: Docker containerization with AWS cloud infrastructure
- **Database**: PostgreSQL with PGVector for embeddings
- **Models**: SEA-LION LLM with AWS Bedrock integration
- **Storage**: AWS S3 for document management

### Update Strategy
- **Upstream Sync**: Regular integration of Open WebUI updates
- **Custom Preservation**: Layered customization approach maintains upgrade compatibility
- **Version Control**: Branch-based development with upstream tracking

## Development Team

**Project**: Ajuda Digital  
**Team**: Timorese Youth Developers  
**Focus**: Government Service Innovation for Timor-Leste  
**Status**: Active Development

## Acknowledgments

We acknowledge and appreciate the Open WebUI project's foundational contribution to the open-source AI community. This implementation builds upon their excellent platform while adapting it for the specific needs of Timor-Leste's government services.

## Links & References

- **Original Project**: [Open WebUI](https://github.com/open-webui/open-webui)
- **Project Website**: https://www.ajuda-digital.com
- **Demo Platform**: https://chat.ajuda-digital.com
## Contact

For questions about this implementation or collaboration opportunities:
- **Project Email**: cs.ajuda.digital@gmail.com
- **Competition Track**: Public Sector - Government Services
