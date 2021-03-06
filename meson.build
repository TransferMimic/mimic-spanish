project('mimic-spanish', 'c', default_options : ['c_std=c99'], version: '0')

build_plugin = get_option('plugin')

sources = [
  'src/es_intonation.c',
  'src/es_text_analysis.c',
  'src/es_init.c',
  'src/es_lexical_insertion.c',
  'src/es_lang_internal.h',
  'src/es_guess_pos.c',
  'src/es_phoneset.c',
  'src/es_pos_tagger.c',
  'src/es_tokenizer.c'
]

includes = [include_directories('include'), include_directories('.')]

# Core Dependency:
mimiccore = dependency('ttsmimic_core', required: false)
if mimiccore.found()
  mimic_plugin_dir = mimiccore.get_pkgconfig_variable('mimic_plugin_dir')
else
  # We compile it:
  mimic_core_sub = subproject('mimic-core')
  mimiccore = mimic_core_sub.get_variable('ttsmimic_core_dep')
  mimic_plugin_dir = mimic_core_sub.get_variable('mimic_plugin_dir')
endif

# If mimic-core does not have plugin support, disable the plugin
if mimic_plugin_dir == ''
    build_plugin = false
endif
message('Build mimic-spanish plugin: @0@'.format(build_plugin))

# Saga Dependency:
saga_dep = dependency('saga', required: false)
if not saga_dep.found()
  # We compile it:
  saga_sub = subproject('saga')
  saga_dep = saga_sub.get_variable('sagashareddep')
endif



es_header_subdir = join_paths('ttsmimic', 'lang', 'es')

ttsmimic_spanish_lib = library('ttsmimic_spanish', sources,
                               include_directories : includes,
                               dependencies: [mimiccore, saga_dep],
                               install: true)

ttsmimic_spanish_dep = declare_dependency(link_with : ttsmimic_spanish_lib, 
                                          include_directories : includes)


if build_plugin
  ttsmimic_spanish_plugin_lib = library(
    'ttsmimic_spanish_plugin',
    ['src/mimic_spanish_plugin.c'],
    include_directories : includes,
    dependencies: [mimiccore, saga_dep, ttsmimic_spanish_dep],
    install_dir: mimic_plugin_dir,
    install: true)

  ttsmimic_spanish_plugin_dep = declare_dependency(
    link_with : ttsmimic_spanish_plugin_lib, 
    include_directories : includes)
endif



############ Headers #################

ttsmimic_headers = ['include/es_lang.h']

install_headers(ttsmimic_headers, subdir : es_header_subdir)

########### pkg-config #############

pkg = import('pkgconfig')

pkg.generate(libraries : ttsmimic_spanish_lib,
             name : 'ttsmimic_spanish',
             subdirs: es_header_subdir,
             requires: ['ttsmimic_core', 'saga'],
             description: 'Spanish support for text to speech synthesis in mimic.',
             version: meson.project_version())

