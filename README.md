# Binaries for programs and plugins
*.exe
*.exe~
*.dll
*.so
*.dylib

# Test binary, built with `go test -c`
*.test

# Output of the go coverage tool, specifically when used with LiteIDE
*.out

# Dependency directories (remove the comment below to include it)
# vendor/

.idea/
.DS_Store

password.txt
run:
  timeout: 5m

linters:
  disable-all: true
  enable:
    - gosimple
    - govet
    - ineffassign
    - goconst
    - goimports
    - misspell
    - revive
    - unconvert
    - unused

linters-settings:
  misspell:
    locale: US

issues:
  exclude-dirs:
    - .github
    - web
    version: 2

before:
  hooks:
    - npm run build
    - go mod tidy -compat=1.17

builds:
  - env:
      - CGO_ENABLED=0
    goos:
      - linux
      - windows
      - darwin
    ignore:
      - goos: darwin
        goarch: 386
      - goos: windows
        goarch: arm64
    ldflags:
      - -s -w

archives:
  - name_template: >-
      {{- .ProjectName }}_{{.Version}}_
      {{- title .Os }}_
      {{- if eq .Arch "amd64" }}x86_64
      {{- else if eq .Arch "386" }}i386
      {{- else }}{{ .Arch }}{{ end }}
      {{- if .Arm }}v{{ .Arm }}{{ end -}}
    format_overrides:
      - goos: windows
        format: zip

checksum:
  name_template: "checksums.txt"

release:
  prerelease: auto

changelog:
  sort: asc
  filters:
    exclude:
      - "^docs:"
      - "^chore:"
      - "^ci:"
