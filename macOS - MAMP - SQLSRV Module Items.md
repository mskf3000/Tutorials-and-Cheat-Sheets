## macOS - MAMP - SQLSRV Module Items

By Jack Szwergold

### PHP SQLSRV under MAMP running PHP 7.x and Above

Before anything else, make sure your local MAMP install’s binary paths are part of you default PATH. You would do this by adding the following paths to your main PATH in your `~/.bash_profile`:

* **MAMP 5.2 Main `bin/` Path**: `/Applications/MAMP/Library/bin`
* **MAMP 5.2 PHP `bin/` Path**: `/Applications/MAMP/bin/php/php7.2.10/bin`

#### Compile and Install FreeTDS

Do this to install the core ODBC stuff on macOS:

	brew tap microsoft/SQLSRV-release https://github.com/Microsoft/homebrew-SQLSRV-release
	brew update
	brew install --no-sandbox msodbcsql17 SQLSRV-tools

Then install the Unix ODBC stuff:

	brew install unixodbc

Now install the actual PHP modules via PECL:

	pecl install sqlsrv pdo_sqlsrv

And finally add these lines to your PHP config file (`php.ini`):

	; Enable 'Microsoft Drivers for PHP for SQL Server' extension module
	extension = sqlsrv.so
	extension = pdo_sqlsrv.so
	
	; Configuration
	
	;sqlsrv.WarningsReturnAsErrors = 1
	;sqlsrv.LogSeverity = 0
	;sqlsrv.LogSubsystems = 0
	;sqlsrv.ClientBufferMaxKBSize = 10240
	
	;pdo_sqlsrv.log_severity = 0
	;pdo_sqlsrv.client_buffer_max_kb_size = 10240

***

*macOS - MAMP - SQLSRV Module Items (c) by Jack Szwergold; written on Janurary 25, 2019. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*