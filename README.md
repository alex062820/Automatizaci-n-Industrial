# Automatizaci-n-Industrial
Código  adaptado para un sistema de monitoreo en un entorno de automatización industrial. En este caso, el sistema evaluará el estado de una máquina en base a alarmas de temperatura, vibración y presión, determinando la probabilidad de una falla en la máquina o si la operación es normal.

class RedBayesiana {
    constructor() {
        // Probabilidades iniciales de los eventos (Falla vs Operación Normal)
        this.probabilidadEvento = {
            "Falla": 0.15,  // Probabilidad inicial de falla
            "OperacionNormal": 0.85  // Probabilidad inicial de operación normal
        };

        // Probabilidad de que un síntoma ocurra dado un evento
        this.probabilidadSintomaDadoUnEvento = {
            "AlarmaTemperatura": { "Falla": 0.9, "OperacionNormal": 0.2 },
            "AlarmaVibracion": { "Falla": 0.8, "OperacionNormal": 0.3 },
            "AlarmaPresion": { "Falla": 0.85, "OperacionNormal": 0.4 }
        };
    }

    // Método para inferir la probabilidad de un evento dado los síntomas (alarmas)
    inferirProbabilidadEvento(evento, tieneAlarmaTemperatura, tieneAlarmaVibracion, tieneAlarmaPresion) {
        let probEvento = this.probabilidadEvento[evento];
        let probAlarmaTemperatura = this.probabilidadSintomaDadoUnEvento["AlarmaTemperatura"][evento];
        let probAlarmaVibracion = this.probabilidadSintomaDadoUnEvento["AlarmaVibracion"][evento];
        let probAlarmaPresion = this.probabilidadSintomaDadoUnEvento["AlarmaPresion"][evento];

        let unirProb = probEvento;

        // Evaluar la alarma de temperatura
        if (tieneAlarmaTemperatura)
            unirProb *= probAlarmaTemperatura;
        else
            unirProb *= (1 - probAlarmaTemperatura);

        // Evaluar la alarma de vibración
        if (tieneAlarmaVibracion)
            unirProb *= probAlarmaVibracion;
        else
            unirProb *= (1 - probAlarmaVibracion);

        // Evaluar la alarma de presión
        if (tieneAlarmaPresion)
            unirProb *= probAlarmaPresion;
        else
            unirProb *= (1 - probAlarmaPresion);

        return unirProb;
    }

    // Método para diagnosticar la probabilidad de Falla vs Operación Normal
    diagnostico(tieneAlarmaTemperatura, tieneAlarmaVibracion, tieneAlarmaPresion) {
        let fallaProb = this.inferirProbabilidadEvento("Falla", tieneAlarmaTemperatura, tieneAlarmaVibracion, tieneAlarmaPresion);
        let operacionNormalProb = this.inferirProbabilidadEvento("OperacionNormal", tieneAlarmaTemperatura, tieneAlarmaVibracion, tieneAlarmaPresion);

        let totalProb = fallaProb + operacionNormalProb;

        fallaProb /= totalProb;
        operacionNormalProb /= totalProb;

        console.log(`Probabilidad de Falla: ${(fallaProb * 100).toFixed(2)}%`);
        console.log(`Probabilidad de Operación Normal: ${(operacionNormalProb * 100).toFixed(2)}%`);
    }
}

// Ejemplo de uso para monitorear la máquina
let red = new RedBayesiana();

let tieneAlarmaTemperatura = true;  // Alarma activada por alta temperatura
let tieneAlarmaVibracion = false;  // Sin alarma de vibración
let tieneAlarmaPresion = true;  // Alarma activada por presión alta

console.log("Diagnóstico basado en las alarmas de temperatura, vibración y presión:");
red.diagnostico(tieneAlarmaTemperatura, tieneAlarmaVibracion, tieneAlarmaPresion);
