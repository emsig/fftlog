project('fftlog', 'c')
# fortran_std=legacy?
add_languages('fortran')

if meson.get_compiler('fortran').get_id() == 'gcc'
    # Allow argument mismatch, otherwise (newer, probably >= 10) gfortran throws
    # Error: Type mismatch in argument ‘ifac’ at (1); passed REAL(8) to INTEGER(4)
    add_global_arguments('-fallow-argument-mismatch',
    language: ['fortran'])
endif

py = import('python')
py3 = py.find_installation()

f2py_name = '_fftlog'
f2py_module_c = f'@f2py_name@module.c'
f2py_wrapper_f = f'@f2py_name@-f2pywrappers.f'
f2py_dep = custom_target(
    'f2py wrappers',
    input: 'src/fftlog.pyf',
    output: [f2py_module_c, f2py_wrapper_f],
    command: [py3, '-m', 'numpy.f2py', '@INPUT@',
    '--build-dir', '@OUTDIR@', ],
)

incdir_numpy = run_command(py3,
    ['-c', 'import os; os.chdir(".."); import numpy; print(numpy.get_include())'],
    check : true
).stdout().strip()
incdir_f2py = run_command(py3,
    ['-c', 'import os; os.chdir(".."); import numpy.f2py; print(numpy.f2py.get_include())'],
    check : true
).stdout().strip()
inc_np = include_directories(incdir_numpy, incdir_f2py)

py3.extension_module('_fftlog',
    [f2py_dep,
     incdir_f2py / 'fortranobject.c',
     'src/cdgamma.f',
     'src/drfftb.f',
     'src/drfftf.f',
     'src/drffti.f',
     'src/fftlog.f',
     ],
    link_language: 'fortran',
    include_directories: inc_np,
    install : true,
    subdir: 'fftlog',
)

py3.install_sources([
    'src/__init__.py',
    ],
    pure: false,
    subdir: 'fftlog',
)
