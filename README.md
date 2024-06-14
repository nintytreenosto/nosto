# nosto
it('configAuthentication should overwrite only non-scoped', async () => {
    fs.writeFileSync(rcFile, `registry=NNN${os.EOL}@myscope:registry=MMM`);
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `@myscope:registry=MMM${os.EOL}//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });

  it('configAuthentication should add non-scoped to scoped', async () => {
    fs.writeFileSync(rcFile, '@myscope:registry=NNN');
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `@myscope:registry=NNN${os.EOL}//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });

  it('configAuthentication should overwrite scoped with scoped', async () => {
    process.env['INPUT_SCOPE'] = 'myscope';
    fs.writeFileSync(rcFile, `@myscope:registry=NNN`);
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}@myscope:registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });

  it('configAuthentication should overwrite only scoped', async () => {
    process.env['INPUT_SCOPE'] = 'myscope';
    fs.writeFileSync(rcFile, `registry=NNN${os.EOL}@myscope:registry=MMM`);
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `registry=NNN${os.EOL}//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}@myscope:registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });

  it('configAuthentication should add scoped to non-scoped', async () => {
    process.env['INPUT_SCOPE'] = 'myscope';
    fs.writeFileSync(rcFile, `registry=MMM`);
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `registry=MMM${os.EOL}//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}@myscope:registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });

  it('configAuthentication should overwrite only one scoped', async () => {
    process.env['INPUT_SCOPE'] = 'myscope';
    fs.writeFileSync(
      rcFile,
      `@otherscope:registry=NNN${os.EOL}@myscope:registry=MMM`
    );
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `@otherscope:registry=NNN${os.EOL}//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}@myscope:registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });

  it('configAuthentication should add scoped to another scoped', async () => {
    process.env['INPUT_SCOPE'] = 'myscope';
    fs.writeFileSync(rcFile, `@otherscope:registry=MMM`);
    await auth.configAuthentication('https://registry.npmjs.org/', 'true');
    const contents = fs.readFileSync(rcFile, {encoding: 'utf8'});
    expect(contents).toBe(
      `@otherscope:registry=MMM${os.EOL}//registry.npmjs.org/:_authToken=\${NODE_AUTH_TOKEN}${os.EOL}@myscope:registry=https://registry.npmjs.org/${os.EOL}always-auth=true`
    );
  });
}));
