# CORIOLIX Documentation

[![Build Status](https://github.com/R-DESC/CORIOLIX_docs/workflows/ci/badge.svg)](https://github.com/R-DESC/CORIOLIX_docs/actions)
[![Documentation](https://img.shields.io/badge/docs-latest-blue.svg)](https://r-desc.github.io/CORIOLIX_docs/)

This repository contains the official documentation for CORIOLIX (**C**ruise **O**bservation **R**eal-Time **I**nterface for **L**ive **I**nformation **E**xchange), a real-time oceanographic data acquisition and processing system for research vessels.

## About CORIOLIX

CORIOLIX was developed during the construction of the Regional Class Research Vessels (RCRVs) for the US Academic Research Fleet (ARF) and established as an ARF Specialized Service Pilot in 2023. The system provides near real-time access to vessel data through a comprehensive web interface.

## Documentation Structure

- **Getting Started**: System access, account setup, and basic usage
- **User Interface**: Detailed guides for all web interface components
- **API Reference**: Complete API documentation and usage examples
- **Administration**: Installation, upgrade, and maintenance procedures

## Local Development

### Prerequisites

- Python 3.8+
- pip

### Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/R-DESC/CORIOLIX_docs.git
   cd CORIOLIX_docs
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Serve the documentation locally:
   ```bash
   mkdocs serve
   ```

4. Open your browser to `http://127.0.0.1:8000`

### Building

To build the static site:

```bash
mkdocs build
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test locally with `mkdocs serve`
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## Related Repositories

- [CORIOLIX_app](https://github.com/R-DESC/CORIOLIX_app) - Main application
- [CORIOLIX_install](https://github.com/R-DESC/CORIOLIX_install) - Installation scripts

## License

This project is part of the US Academic Research Fleet infrastructure. Please contact R-DESC for licensing information.

## Support

For questions or support, please:
- Check the [documentation](https://r-desc.github.io/CORIOLIX_docs/)
- Open an issue in this repository
- Contact the R-DESC team
