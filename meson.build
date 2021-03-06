project('Arduino SDK Logger',
	['c', 'cpp'],
	default_options : [
		# `build.*` options affect `native: true targets`
		# plain options affect `native: false` targets.
		'cpp_std=c++11', 'build.cpp_std=c++11'
	],
	license: 'MIT',
	meson_version: '>0.51.0',
	version: '0.9')

########################
# Identify Compiler(s) #
########################

# Identify compilers & get flags
subdir('build/compiler')

native_compiler = meson.get_compiler('cpp', native: true)
host_compiler = meson.get_compiler('cpp')

add_project_arguments(native_cpp_compiler_warnings, language: 'cpp', native: true)
add_project_arguments(target_cpp_compiler_warnings, language: 'cpp', native: false)

#########################
# Process Build Options #
#########################

if get_option('enable-werror') == true
	add_project_arguments('-Werror', language : ['cpp'])
endif

if get_option('enable-pedantic-error') == true
	add_project_arguments('-pedantic-error', language : ['cpp'])
endif


#######################
# Process source tree #
#######################

test_includes = [
	include_directories('test'),
	include_directories('src'),
]

logging_tests = executable('arduino_logger_tests',
	[
		files('src/ArduinoLogger.cpp'),
		files('test/CircularBufferLoggerTests.cpp'),
		files('test/AVRCircularBufferLoggerTests.cpp'),
		files('test/catch_main.cpp'),
		files('test/printf.c'),
		files('test/test_helper.cpp'),
		files('test/CoreLoggerTests.cpp'),
	],
	include_directories: test_includes,
)

test('ArduinoLogger_tests',
	logging_tests)

############################
# Supporting Build Targets #
############################

run_target('complexity',
	command: ['tools/complexity_check.sh', '-w'])

run_target('complexity-list',
	command: ['tools/complexity_check.sh'])

cppcheck = find_program('cppcheck', required: false)

if cppcheck.found()
	run_target('cppcheck',
		command: ['cppcheck',
		# Options
		'--quiet', '--enable=style', '--force',
		# Include Directories
		'-I', meson.current_source_dir() + '/include',
		# Source Directories
		'src']
	)

	run_target('cppcheck-xml',
		command: ['tools/cppcheck_xml.sh']
	)
endif

clangtidy = find_program('clang-tidy', required: false)

if clangtidy.found()
    run_target(
        'tidy',
        command: [
            'build/tooling/clang-tidy/clang-tidy.sh',
            # Checks
            '-*,cppcoreguidelines-*,modernize-*,performance-*,portability-*,readability-*,' +
            '-readability-inconsistent-declaration-parameter-name,' +
            # Header Filter
            '.*',
            #Finish with files
        ] + [files('src/ArduinoLogger.cpp')])
endif
