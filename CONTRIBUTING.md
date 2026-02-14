# Contributing to S3 Mini Voice Satellite

Thank you for your interest in contributing to the S3 Mini Voice Satellite project! This document provides guidelines and instructions for contributing.

## Code of Conduct

This project adheres to a code of conduct that all contributors are expected to follow:

- Be respectful and inclusive
- Welcome newcomers and help them get started
- Focus on what is best for the community
- Show empathy towards other community members

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues to avoid duplicates. When creating a bug report, include as many details as possible:

**Bug Report Template:**

```markdown
**Describe the bug**
A clear description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. See error

**Expected behavior**
What you expected to happen.

**Actual behavior**
What actually happened.

**Environment:**
- ESPHome version:
- Home Assistant version:
- Hardware version:
- Firmware version:

**Logs**
Paste relevant ESPHome/Home Assistant logs here.

**Additional context**
Any other context about the problem.
```

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- Use a clear and descriptive title
- Provide a detailed description of the proposed enhancement
- Explain why this enhancement would be useful
- List any alternative solutions you've considered

### Pull Requests

1. **Fork the repository** and create your branch from `main`
2. **Make your changes** following the coding standards below
3. **Test your changes** thoroughly
4. **Update documentation** if needed
5. **Commit your changes** with clear commit messages
6. **Push to your fork** and submit a pull request

#### Pull Request Guidelines

- Fill in the pull request template
- Reference any related issues
- Include screenshots for UI changes
- Ensure CI/CD checks pass
- Request review from maintainers

## Development Setup

### Prerequisites

- Python 3.9 or later
- ESPHome installed (`pip install esphome`)
- ESP32-S3 development board
- Required hardware components (see README)

### Local Development

1. **Clone your fork:**
   ```bash
   git clone https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite.git
   cd S3-Mini-Voice-Satellite
   ```

2. **Create secrets file:**
   ```bash
   cp secrets.yaml.example secrets.yaml
   # Edit secrets.yaml with your credentials
   ```

3. **Validate configuration:**
   ```bash
   esphome config s3-mini-voice-satellite.yaml
   ```

4. **Compile firmware:**
   ```bash
   esphome compile s3-mini-voice-satellite.yaml
   ```

5. **Upload to device:**
   ```bash
   esphome run s3-mini-voice-satellite.yaml
   ```

### Testing Changes

Before submitting a pull request:

- [ ] Configuration validates without errors
- [ ] Firmware compiles successfully
- [ ] Tested on actual hardware
- [ ] LED indicators work correctly
- [ ] Microphone captures audio
- [ ] Speaker plays audio
- [ ] Wake word detection works
- [ ] Home Assistant integration functions
- [ ] OTA updates work
- [ ] Documentation updated

## Coding Standards

### YAML Configuration

- Use 2 spaces for indentation
- Keep lines under 100 characters when possible
- Comment complex configurations
- Group related settings together
- Use descriptive names for components

**Example:**
```yaml
# I2S Microphone Configuration (INMP441)
microphone:
  - platform: i2s_audio
    id: external_mic
    adc_type: external
    i2s_din_pin: ${mic_i2s_sd}
    pdm: false
    channel: left
    sample_rate: 16000
    bits_per_sample: 32bit
```

### Documentation

- Use clear, concise language
- Include code examples where helpful
- Add screenshots for visual processes
- Keep README.md up to date
- Document all pin assignments
- Explain configuration options

### Commit Messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

**Examples:**
```
feat(audio): add volume control via Home Assistant

- Add number entity for volume control
- Update speaker configuration
- Document volume settings in README

Closes #42
```

```
fix(led): correct GPIO pin for RGB LED

The RGB LED was incorrectly assigned to GPIO48.
Changed to GPIO47 per S3 Mini datasheet.

Fixes #38
```

## Documentation

### README.md

Update the README when:
- Adding new features
- Changing hardware requirements
- Modifying installation steps
- Updating troubleshooting guide

### WIRING.md

Update wiring guide when:
- Changing pin assignments
- Adding new components
- Modifying power requirements
- Adding alternative configurations

### CHANGELOG.md

Add entries to CHANGELOG.md for all notable changes:
- New features
- Bug fixes
- Breaking changes
- Deprecations

## Hardware Contributions

### Testing New Components

If you're testing alternative hardware:

1. Document the component specifications
2. Verify pin compatibility
3. Test all functionality
4. Update wiring guide with alternatives
5. Add to BOM with purchase links
6. Share results in discussions

### 3D Models and Enclosures

Contributions of enclosure designs are welcome:

1. Provide STL files for 3D printing
2. Include source files (Fusion 360, FreeCAD, etc.)
3. Add assembly instructions
4. Include photos of assembled enclosure
5. Document print settings
6. List any additional hardware needed (screws, etc.)

## Community

### Discussions

Use [GitHub Discussions](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/discussions) for:
- Questions about usage
- Sharing your builds
- Proposing ideas
- General community chat

### Issues

Use [GitHub Issues](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/issues) for:
- Bug reports
- Feature requests
- Documentation improvements
- Hardware compatibility issues

## Release Process

Maintainers follow this process for releases:

1. Update version in `s3-mini-voice-satellite.yaml`
2. Update CHANGELOG.md
3. Create git tag: `git tag -a v1.x.x -m "Release v1.x.x"`
4. Push tag: `git push origin v1.x.x`
5. GitHub Actions builds and creates release
6. Update web installer links
7. Announce in discussions

## Recognition

Contributors are recognized in:
- GitHub contributors page
- README.md credits section
- Release notes
- Community discussions

## Questions?

If you have questions about contributing:

- Check existing documentation
- Search closed issues
- Ask in GitHub Discussions
- Tag maintainers in issues

Thank you for contributing to making voice assistants accessible to everyone! 🎤✨
