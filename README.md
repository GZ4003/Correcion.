# Correcion.


#!/bin/bash

# Función para registrar en el syslog
log_syslog() {
    logger -p syslog.info "$1"
}

# Crear grupos
grupos=("desarrollo" "operaciones" "ingenieria")
for grupo in "${grupos[@]}"; do
    if ! getent group "$grupo" > /dev/null 2>&1; then
        sudo groupadd "$grupo"
        log_syslog "Groupadd $grupo - Se creó el grupo $grupo"
    else
        log_syslog "Groupadd $grupo - El grupo $grupo ya existe"
    fi
done

# Crear usuarios y asignar contraseñas
declare -A usuarios
usuarios=(
    [gfernandez]="desarrollo"
    [jmendez]="desarrollo"
    [pquiroga]="operaciones"
    [icoca]="operaciones"
    [jfruta]="ingenieria"
    [camarillo]="ingenieria"
)

contraseñas=(
    [gfernandez]="fer651ge32"
    [jmendez]="fg16f8we2f"
    [pquiroga]="g16e5r1g3r"
    [icoca]="65rf1gedfg61"
    [jfruta]="1g6e5r1g3wry"
    [camarillo]="th6wik5jsw8"
)

for usuario in "${!usuarios[@]}"; do
    grupo="${usuarios[$usuario]}"
    contraseña="${contraseñas[$usuario]}"
    
    if ! id "$usuario" > /dev/null 2>&1; then
        sudo useradd -m -g "$grupo" -s /bin/bash "$usuario"
        echo "$usuario:$contraseña" | sudo chpasswd
        log_syslog "Useradd $usuario - Se creó el usuario $usuario en el grupo $grupo"
    else
        log_syslog "Useradd $usuario - El usuario $usuario ya existe"
    fi
done

echo "Proceso completado.
