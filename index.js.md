/**
 * Effective-System Core Architecture
 * Refactored per architecture review: No side effects, decoupled init, explicit error routing.
 */

const CONFIG = {
    ENV: process.env.NODE_ENV || 'production',
    FAIL_FAST: true
};

class SystemEngine {
    constructor() {
        // Fix #1: Constructor only sets state. Decoupled from execution flow.
        this.active = false;
    }

    // Explicit lifecycle initialization method
    init() {
        if (this.active) {
            throw new Error('[Fatal] Re-initialization blocked.');
        }
        console.log(`[Engine] Online | Mode: ${CONFIG.ENV}`);
        this.active = true;
    }

    // Fix #2: Pure operational logic. Redundant internal try/catch and process.exit() removed.
    run() {
        if (!this.active) {
            throw new Error('[Engine] Cannot run. Engine not initialized.');
        }
        console.log('[Engine] Operational state check verified.');
    }
}

// Fix #3: Avoid top-level side effects. Explicitly manage the instance.
const startEngine = () => {
    try {
        const engine = new SystemEngine();
        engine.init();
        engine.run();
    } catch (error) {
        console.error(`[Fail-Fast Tripped] Global Error Catch: ${error.message}`);
        process.exit(1); // Single point of process termination
    }
};

// Entry Point
startEngine();
